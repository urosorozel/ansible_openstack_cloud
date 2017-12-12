# ansible_openstack_cloud
Deploy multinode Openstack on Cloud (vxlan based netwok)

```
# controller 3
ip link add vxlan100 type vxlan id 100 dstport 4789 local 10.176.160.104 
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.129.217
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.129.203
ip link set dev eth0 mtu 1450
ip link set  dev vxlan100 up
ip addr add 10.20.10.2/24 dev vxlan100

# controller 2
ip link del dev vxlan100
ip link add vxlan100 type vxlan id 100 dstport 4789 local 10.176.129.217
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.160.104 
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.129.203
ip link set dev eth0 mtu 1450
ip link set  dev vxlan100 up
ip addr add 10.20.10.3/24 dev vxlan100

# controller 1 
ip link del dev vxlan100
ip link add vxlan100 type vxlan id 100 dstport 4789 local 10.176.129.203
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.160.104 
bridge fdb append 00:00:00:00:00:00 dev vxlan100 dst 10.176.129.217
ip link set dev eth0 mtu 1450
ip link set  dev vxlan100 up
ip addr add 10.20.10.4/24 dev vxlan100
```
https://vincent.bernat.im/en/blog/2017-vxlan-linux

https://joejulian.name/blog/how-to-configure-linux-vxlans-with-multiple-unicast-endpoints/

ansible-playbook site.yml  --tags firewall,bmc
