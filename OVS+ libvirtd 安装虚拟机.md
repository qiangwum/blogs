```
sudo apt install -y openvswitch-switch uml-utilities dnsmasq 

systemctl start openvswitch-switch.service

---------------------------------------------------------------------------------------


ovs-vsctl add-br br-fn-ovs
ovs-vsctl add-port br-fn-ovs qdhcp -- set interface qdhcp type=internal

ovs-vsctl set port qdhcp tag xxx

ip addr add x.x.x.x/28 dev qdhcp

ip link set qdhcp up

echo "interface=qdhcp" >> /etc/dnsmasq.conf
echo "except-interface=lo" >> /etc/dnsmasq.conf
echo "dhcp-range=x.x.x.x,x.x.x.x,12h" >> /etc/dnsmasq.conf

echo "dhcp-host=52:54:00:d0:be:01,x.x.x.x" >> /etc/dnsmasq.conf
echo "dhcp-host=52:54:00:d0:be:02,x.x.x.x" >> /etc/dnsmasq.conf
echo "dhcp-host=52:54:00:d0:be:03,x.x.x.x" >> /etc/dnsmasq.conf

systemctl enable dnsmasq
systemctl start dnsmasq


cat >br-fn-ovs.xml<<EOF
<network>
  <name>br-fn-ovs</name>
  <forward mode='bridge'/>
  <bridge name='br-fn-ovs'/>
  <model type='virtio'/>
  <virtualport type='openvswitch'/>
</network>
EOF


virsh net-define --file br-fn-ovs.xml
virsh net-start br-fn-ovs
virsh net-autostart br-fn-ovs

virt-install --virt-type qemu --name fn-M --memory 2048 --vcpus 2 --disk /var/lib/libvirt/qemu/xxxM.qcow2,format=qcow2,bus=virtio,size=10 --import --os-variant ubuntu20.04 --noautoconsole --network network=br-fn-ovs,mac=52:54:00:d0:be:01,target=tap0

virt-install  --virt-type qemu --name fn-S1 --memory 2048 --vcpus 2 --disk /var/lib/libvirt/qemu/xxxS1.qcow2,format=qcow2,bus=virtio,size=10 --import --os-variant ubuntu20.04 --noautoconsole --network network=br-fn-ovs,mac=52:54:00:d0:be:02,target=tap1

virt-install --virt-type qemu --name fn-S2 --memory 2048 --vcpus 2 --disk /var/lib/libvirt/qemu/xxxS2.qcow2,format=qcow2,bus=virtio,size=10 --import --os-variant ubuntu20.04 --noautoconsole --network network=br-fn-ovs,mac=52:54:00:d0:be:03,target=tap2

```
