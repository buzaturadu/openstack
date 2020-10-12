#Openstack deployement 

	sudo su -

	yum update

	yum install -y qemu-kvm qemu-img virt-manager libvirt libvirt-python libvirt-client virt-install virt-viewer bridge-utils ibguestfs-tools wget vim
 

	systemctl start libvirtd

	systemctl enable libvirtd




	cp /etc/sysconfig/network-scripts/ifcfg-eth0 vim /etc/sysconfig/network-scripts/ifcfg-eth1


	vim /etc/sysconfig/network-scripts/ifcfg-eth1

change name to eth1

	vim net.xml

Paste

	<network>
	  <name>ctlplane</name>
	  <uuid>bd071956-3e1e-4ce7-81cc-d9f65460e1c2</uuid>
	  <bridge name='virbr1' stp='on' delay='0'/>
	  <mac address='52:54:00:e4:7d:c8'/>
	  <ip address='192.168.123.2' netmask='255.255.255.0'>
	  </ip>
	</network>


Create network

	virsh net-define net.xml

	virsh net-list (to verify if is created)

	virsh net-autostart ctlplane

	virsh net-start ctlplane

create vm xml

	vim vm.xml

<domain type='kvm'>
  <name>dir</name>
  <uuid>b7eef997-8db2-4a9b-81c0-d1619e14437a</uuid>
  <memory unit='KiB'>16195712</memory>
  <currentMemory unit='KiB'>16194304</currentMemory>
  <vcpu placement='static'>7</vcpu>
  <resource>
    <partition>/machine</partition>
  </resource>
  <os>
    <type arch='x86_64' machine='pc-i440fx-rhel7.0.0'>hvm</type>
    <boot dev='hd'/>
    <bootmenu enable='yes'/>
  </os>
  <features>
    <acpi/>
    <apic/>
  </features>
  <cpu mode='host-passthrough' check='full'>
    <feature policy='require' name='vme'/>
    <feature policy='disable' name='ds'/>
    <feature policy='disable' name='acpi'/>
    <feature policy='require' name='ss'/>
    <feature policy='disable' name='ht'/>
    <feature policy='disable' name='tm'/>
    <feature policy='disable' name='pbe'/>
    <feature policy='disable' name='dtes64'/>
    <feature policy='disable' name='monitor'/>
    <feature policy='disable' name='ds_cpl'/>
    <feature policy='require' name='vmx'/>
    <feature policy='disable' name='smx'/>
    <feature policy='disable' name='est'/>
    <feature policy='disable' name='tm2'/>
    <feature policy='disable' name='xtpr'/>
    <feature policy='disable' name='pdcm'/>
    <feature policy='disable' name='dca'/>
    <feature policy='disable' name='osxsave'/>
    <feature policy='require' name='f16c'/>
    <feature policy='require' name='rdrand'/>
    <feature policy='disable' name='arat'/>
    <feature policy='disable' name='tsc_adjust'/>
    <feature policy='require' name='md-clear'/>
    <feature policy='require' name='stibp'/>
    <feature policy='require' name='ssbd'/>
    <feature policy='require' name='xsaveopt'/>
    <feature policy='require' name='pdpe1gb'/>
    <feature policy='require' name='abm'/>
    <feature policy='disable' name='hle'/>
    <feature policy='disable' name='rtm'/>
    <feature policy='require' name='hypervisor'/>
  </cpu>
  <clock offset='utc'>
    <timer name='rtc' tickpolicy='catchup'/>
    <timer name='pit' tickpolicy='delay'/>
    <timer name='hpet' present='no'/>
  </clock>
  <on_poweroff>destroy</on_poweroff>
  <on_reboot>restart</on_reboot>
  <on_crash>destroy</on_crash>
  <pm>
    <suspend-to-mem enabled='no'/>
    <suspend-to-disk enabled='no'/>
  </pm>
  <devices>
    <emulator>/usr/libexec/qemu-kvm</emulator>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2' cache='none' io='native'/>
      <source file='/home/vms/dir.qcow2'/>
      <target dev='vda' bus='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
    </disk>
    <controller type='scsi' index='0' model='virtio-scsi'>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x04' function='0x0'/>
    </controller>
    <controller type='usb' index='0' model='ich9-ehci1'>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x7'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci1'>
      <master startport='0'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0' multifunction='on'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci2'>
      <master startport='2'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x1'/>
    </controller>
    <controller type='usb' index='0' model='ich9-uhci3'>
      <master startport='4'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x2'/>
    </controller>
    <controller type='pci' index='0' model='pci-root'/>
    <interface type='network'>
      <mac address='52:54:00:00:00:20'/>
      <source network='default'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x08' function='0x0'/>
    </interface>
    <interface type='network'>
      <mac address='52:54:00:00:00:10'/>
      <source network='ctlplane'/>
      <model type='virtio'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
    </interface>
    <serial type='pty'>
      <target type='isa-serial' port='0'>
        <model name='isa-serial'/>
      </target>
    </serial>
    <console type='pty'>
      <target type='serial' port='0'/>
    </console>
    <input type='mouse' bus='ps2'/>
    <input type='keyboard' bus='ps2'/>
    <graphics type='vnc' port='-1' autoport='yes' listen='127.0.0.1'>
      <listen type='address' address='127.0.0.1'/>
    </graphics>
    <video>
      <model type='cirrus' vram='16384' heads='1' primary='yes'/>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x02' function='0x0'/>
    </video>
    <memballoon model='virtio'>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x07' function='0x0'/>
    </memballoon>
  </devices>
  <seclabel type='dynamic' model='selinux' relabel='yes'/>
  <seclabel type='dynamic' model='dac' relabel='yes'/>
</domain>


Create folder for vm disk


	mkdir home/vms

Create VM


	virsh define vm.xml


	qemu-img create -f qcow2 /home/vms/dir/qcow2 100G

	wget https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2

	export LIBGUESTFS_BACKEND=direct  

	virt-resize --expand /dev/sda1 CentOS-7-x86_64-GenericCloud.qcow2 /home/vms/dir.qcow2

	virt-customize -a /home/vms/dir.qcow2 --root-password password:redhat12

	sudo virt-sysprep -a /home/vms/dir.qcow2 --ssh-inject centos:file:/root/.ssh/id_rsa.pub

Start the VM


	virsh start dir

If permission denied try rebooting the server

	virsh console dir

Login using root and redhat12
#do not update the VM


virt console dir

	xfs_growfs  /dev/vda1


cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1

##Maybe
systemctl disable cloud-init; systemctl disable cloud-init-local; systemctl disable cloud-config;systemctl disable cloud-final
ssh-heygen
/usr/bin/ssh-keygen -A
rpm -qf /usr/sbin/sshd
openssh-7.2p2-74.16.3.x86_64
rpm -V openssh-7.2p2-74.16.3.x86_64



Manage network

	cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1

Change interface name th eth1

	vi /etc/sysconfig/network-scripts/ifcfg-eth1 

	if up eth1

Disabling SELinux
vi /etc/selinux/config  
Set from Enforcing to Disabled


Exit console and ssh to vm


	ssh root@192.168.122.58

	sudo yum install vim wget

	hostnamectl set-hostname "undercloud.example.com

	exec bash

	echo "192.168.123.1 undercloud.example.com" >> /etc/hosts

Create user

	useradd stack

	echo "enter_password_here" | passwd --stdin stack

	echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack

	chmod 0440 /etc/sudoers.d/stack

	su - stack

Instal repo


	sudo yum install -y https://trunk.rdoproject.org/centos7/current/python2-tripleo-repos-0.0.1-0.20200409224957.8bac392.el7.noarch.rpm  
 
	sudo -E tripleo-repos -b queens current

	sudo yum install -y python-tripleoclient


Create undercloud config


	vim undercloud.conf


	[DEFAULT]
undercloud_hostname=undercloud.radu.test
local_ip = 172.25.250.10/24
network_gateway = 172.25.250.10
local_interface = eth1
network_cidr = 172.25.250.0/24
masquerade_network = 172.25.250.0/24
dhcp_start = 172.25.250.5
dhcp_end = 172.25.250.25
inspection_interface = br-ctlplane
inspection_iprange = 172.25.250.100,172.25.250.150
undercloud_debug = true
enable_tempest = true
enable_telemetry = false
ipxe_enabled = true
store_events = true


Install openstack


	openstack undercloud install



Verify the OpenStack Service list

	source stackrc

Login to the undercloud server as stack user and download the overcloud images


	sudo wget https://images.rdoproject.org/queens/delorean/current-tripleo-rdo/undercloud.qcow2

