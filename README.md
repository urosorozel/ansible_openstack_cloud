# ansible_openstack_cloud
Deploy multinode Openstack on Cloud (vxlan based netwok)


1. Prepare your laptop or server where you will run ansible
 
virtualenv rackspace
source rackspace/bin/activate
pip install pyrax
pip install ansible
export RAX_CREDS_FILE=~/.pyrax.cfg
ansible-galaxy install -r ansible-role-requirements.txt
 
2. Clone ansible playbooks
git clone https://github.com/urosorozel/ansible_openstack_cloud.git
 
3. Define unique server hostnames in host_vars/localhost.yml and reflect changes to filenames in host_vars.
Configure ansible.cfg sample is provided in ansible.cfg.sample
 
4. Create servers (5 servers will be created and 3 volumes will be attached to infra nodes for swift
```ansible-playbook build_servers.yml```
 
5. Run deploy playbook
```ansible-playbook site.yml```
 
6. Run post deploy playbook
```ansible-playbook post_deploy.yml```
 

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
