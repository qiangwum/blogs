因为image-cache功能打开了：
<https://docs.openstack.org/cinder/xena/admin/blockstorage-image-volume-cache.html>

### 第一次镜像创建卷流程：
1.下载镜像从本地到backend.  
2.创建一个空卷.大小通过round_to_num_gran计算64GB.  
3.把镜像拷贝到这个空卷里.  
4.镜像格式转换.  
5.然后后再通过这个卷克隆 iamge-cache卷因为没有指定size.所以大小也是64（此时原卷的status是download）  
6.日志里可以看到对source卷extend to 60，但是算法还是之前的算法，所以仍然是64GiB.  
7.等待image-cache卷创建完成，source 卷status 更新为active，此时两卷都创建成功  

* 下面是从日志中抓取的流程
```sh
1.cinder.volume.flows.manager.create_volume                 #being created as image with specification
2.cinder.image.image_utils                                                 #Image download
3.cinder.volume.drivers.dell_emc.powerflex.rest_client      #Domain ID, Pool ID
4.cinder.volume.drivers.dell_emc.powerflex.driver            #Successfully created volume 362410e4-454e-4dbc-8508-80912fb3b68b. Volume size: 64. PowerFlex volume name: NiQQ5EVOTbyFCICRL7O2iw==, id: f02aebf300000001.
5.cinder.volume.drivers.dell_emc.powerflex.driver            #Copy image e7aba6b9-7eaf-4ffa-b435-450d80b7a1e9 from image service <cinder.image.glance.GlanceImageService object at 0x7f2d781cc390> to volume 362410e4-454e-4dbc-8508-80912fb3b68b.
6.cinder.volume.drivers.dell_emc.powerflex                     #Call os-brick to attach PowerFlex volume.driver
7.os_brick.initiator.connectors.scaleio                              ··· ···
8.cinder.image.image_utils                                                #Converted 61440.00 MB image at 408.04 MB/s
9.cinder.volume.drivers.dell_emc.powerflex.driver           #Call os-brick to detach PowerFlex volume
10.os_brick.initiator.connectors.scaleio         ··· ···
11.cinder.volume.flows.manager.create_volume                #Volume 5322569a-4d2d-4e11-a832-8a3c932711e6: being created as source_vol with specification: {'status': 'creating', 'volume_name': 'volume-5322569a-4d2d-4e11-a832-8a3c932711e6', 'volume_size': 64, 'source_volid': '362410e4-454e-4dbc-8508-80912fb3b68b', 'source_volstatus': 'downloading'}
12.cinder.volume.drivers.dell_emc.powerflex.driver          #Clone volume 362410e4-454e-4dbc-8508-80912fb3b68b to 5322569a-4d2d-4e11-a832-8a3c932711e6
13.cinder.volume.drivers.dell_emc.powerflex.driver          #Successfully created volume 5322569a-4d2d-4e11-a832-8a3c932711e6 from source 362410e4-454e-4dbc-8508-80912fb3b68b
14.cinder.volume.flows.manager.create_volume                #Volume volume-5322569a-4d2d-4e11-a832-8a3c932711e6 (5322569a-4d2d-4e11-a832-8a3c932711e6): created successfully
15.cinder.volume.manager                                                 #Created volume successfully.
16.cinder.volume.drivers.dell_emc.powerflex.driver           #Extend volume 362410e4-454e-4dbc-8508-80912fb3b68b to size 60.
17.cinder.volume.flows.manager.create_volume                #Volume volume-362410e4-454e-4dbc-8508-80912fb3b68b (362410e4-454e-4dbc-8508-80912fb3b68b): created successfully
18.cinder.volume.manager                                         #Created volume successfully.
```
```sh
def round_to_num_gran(size, num=8):
    """Round size to nearest value that is multiple of `num`."""

    if size % num == 0:
        return size
    return size + num - (size % num)
```
__60+8-60%8__

第1次克隆卷流程：
```log
1. cinder.volume.flows.manager.create_volume                  #Volume 301cf2db-f33f-45f0-b081-8698ee90c046: being created as image with specification: {'status': 'creating', 'volume_name': 'volume-301cf2db-f33f-45f0-b081-8698ee90c046', 'volume_size': 60, 'image_id': 'e7aba6b9-7eaf-4ffa-b435-450d80b7a1e9', 'image_location': (None, None), 'image_meta':
2. cinder.volume.drivers.dell_emc.powerflex.driver            #Clone volume 5322569a-4d2d-4e11-a832-8a3c932711e6 to 301cf2db-f33f-45f0-b081-8698ee90c046.
3. cinder.volume.drivers.dell_emc.powerflex.driver            #Successfully created volume 301cf2db-f33f-45f0-b081-8698ee90c046 from source 5322569a-4d2d-4e11-a832-8a3c932711e6. PowerFlex volume name: MBzy2/M/RfCwgYaY7pDARg==, id: f02aebf600000009, source name: UyJWmk0tThGoMoo8kycR5g==, source id: f02aebf400000002.
4. cinder.volume.flows.manager.create_volume                  #Volume volume-301cf2db-f33f-45f0-b081-8698ee90c046 (301cf2db-f33f-45f0-b081-8698ee90c046): created successfully
5. cinder.volume.manager                                      #Created volume successfully.
```
```python
        try:
            # Snapshot object does not have 'size' attribute.
            source_size = source.volume_size
        except AttributeError:
            source_size = source.size                      # 如果clone卷没有size，则执行此处，下边if也不执行，使用source.size
        if volume.size > source_size:                      # 如果clone卷有size参数且小于source.size 则不执行这里，使用指定的size
            real_size = flex_utils.round_to_num_gran(volume.size)
            client.extend_volume(provider_id, real_size)
        if volume.is_replicated():
            self._setup_volume_replication(volume, provider_id)
            model_updates["replication_status"] = (
                fields.ReplicationStatus.ENABLED
            )
        return model_updates

```

ISSUE:
>  1. 为什么还要extend volume到real_size，没有实际意义。

>  2. Mitaka版本默认不启用镜像盘缓存功能，需要另外配置。
