### openstack对接多套存储

参考如下帖子

<https://blog.51cto.com/driver2ice/2473970>
<https://docs.openstack.org/cinder/xena/admin/blockstorage-multi-backend.html>
<https://www.mirantis.com/blog/the-first-and-final-word-on-openstack-availability-zones/>

使用volume-type来区分使用的存储，

<!--如果需要分别使用哪些存储，可以添加nova az和cinder az对应的配置,后面用到-->

+ cinder type-create scaleIO1
+ cinder type-key scaleIO1 set volume_backend_name=scaleIO1
+ cinder type-create scaleIO2
+ cinder type-key scaleIO2 set volume_backend_name=scaleIO2



### cinder az和nova az同步问题：
1. 背景：
  在使用OpenStack云平台时，有时因业务架构需要拆分不同的Availability Zone可用域，包括计算Nova和存储Cinder，并期望可以将两个AZ对应起来以便起到隔离的效果。目前多数OpenStack平台或多或少采用了Ceph作为Cinder存储后端（以及Glance、Swift），并在创建虚拟机时选择从镜像创建块存储。我们希望可以通过创建不同的Cinder AZ配置不同的Ceph pool，每个pool关联不同的osd，已便达到不同用户创建的虚拟机操作系统磁盘在Ceph集群中是完全隔离的。
2. 问题
  首先我们启动多个cinder-volume实例，配置不同的storage_availalibility_zone，期望效果是选择虚拟机的AZ时可以匹配Cinder里的AZ，例如：

storage_availability_zone=AZ1
然而实际情况并不理想，在创建虚拟机时，选择了Nova的AZ（比如AZ1），创建出来的卷却在Cinder的nova可用域里，因为Cinder的默认域是nova，最终结果并没有匹配上。实际上，nova在调用cinder的时候并未把虚拟机实例的availalibility_zone的值传过去。

3. 解决
  查看源码/usr/lib/python2.7/site-packages/nova/conf/cinder.py中的设定，发现一个关键参数cross_az_attach，默认值为True，这意味着虚拟机的磁盘可以跨域绑定。
  代码默认允许跨nova cinder az创建卷，在/nova/conf/cinder.py，如需需要配置需要改default=False，华为云是这么配置的。
```
   cfg.BoolOpt('cross_az_attach',
                default=True,
                help="""
```
Allow attach between instance and volume in different availability zones.