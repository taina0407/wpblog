http://doc.opensuse.org/products/opensuse/openSUSE/opensuse-kvm/sec.libvirt.stornet.storage.html
The purpose of storage pools is to provide block devices located on the VM Host Server, that can be added to a VM Guest when managing it from remote

Volumes from a pool attached to VM Guests are always available, regardless of the pool's state (Active or Inactive). The state of the pool solely affects the ability to attach volumes to a VM Guest via remote management.

http://ailoo.net/2011/02/use-a-lvm-volume-group-with-libvirt/
http://docs.fedoraproject.org/en-US/Fedora/13/html/Virtualization_Guide/sect-Virtualization-Virtualized_block_devices-Adding_storage_devices_to_guests.html


https://help.ubuntu.com/11.10/serverguide/C/libvirt.html

查看硬件是否支持kvm

	sudo aptitude install cpu-checker
	$ sudo kvm-ok
	INFO: /dev/kvm does not exist
	HINT:   sudo modprobe kvm_intel
	INFO: Your CPU supports KVM extensions
	KVM acceleration can be used
	$ sudo modprobe kvm_intel
	$ sudo kvm-ok            
	INFO: /dev/kvm exists
	KVM acceleration can be used

Install libvirt

	sudo apt-get install kvm libvirt-bin
	sudo adduser $USER libvirtd
	sudo apt-get install virtinst

配置 bridge networking


首先安装`bridge-utils`

	sudo aptitude install bridge-utils

编辑`/etc/network/interfaces`, 添加如下代码

	auto eth0
	iface eth0 inet manual

	auto br0
	iface br0 inet dhcp
			bridge_ports eth0
			bridge_fd 9
			bridge_hello 2
			bridge_maxage 12
			bridge_stp off

https://help.ubuntu.com/11.10/serverguide/C/jeos-and-vmbuilder.html


	sudo apt-get install python-vm-builder
	wget http://mirrors.sohu.com/ubuntu/pool/universe/v/vm-builder/python-vm-builder_0.12.4+bzr469-0ubuntu1_all.deb
	sudo dpkg -i python-vm-builder_0.12.4+bzr469-0ubuntu1_all.deb

[Using virtio for block devices makes disks and partitions disappear in KVM/QEMU (using vmbuilder and libvirt)](https://bugs.launchpad.net/ubuntu/lucid/+source/vm-builder/+bug/517067)

change the templates

	#for $disk in $disks
		<disk type='file' device='disk'>
		  <source file='$disk.filename' />
		  <target dev='vd$disk.devletters()' bus='virtio' />
		</disk>
	#end for

	the mountall failed messages fail because there *is* no /dev/sdX* and your /etc/fstab says to mount them.
	You've changed to /dev/vda* , /etc/fstab will need to be updated.


http://www.server-world.info/en/note?os=Ubuntu_11.04&p=kvm&f=2

	sudo build-vm.sh web
	sudo virt-clone --original web --name template --file /home/ping/vm/template.img
	sudo virt-clone --original template --name web2 --file /home/ping/vm/web2.img 
	sudo virt-clone --original web --name template_chef --file /home/ping/vm/template_chef.img

	mkdir ~/vm/chefbox
	sudo virt-clone --original basebox --name chefbox --file $HOME/vm/chefbox/system.img --file $HOME/vm/chefbox/data.img
	mkdir ~/vm/webdev
	sudo virt-clone --original basebox --name webdev --file $HOME/vm/webdev/system.img --file $HOME/vm/webdev/data.img

	$ cat copy-vm.sh
	if test -z "$1"
	then
		echo "USAGE: $0 <name>\nExample: bash $0 hybox"
		exit
	fi
	name=$1
	vmdir=$HOME/vm/$name
	mkdir $vmdir
	sudo virt-clone --original basebox --name $name --file $vmdir/system.img --file $vmdir/data.img

emma6

	sudo build-vm.sh basebox
	sudo virt-clone --original basebox --name cheftest --file $HOME/vm/cheftest.img
	# change hostname
	sudo virsh start cheftest --console
	
on dev (chef workstation)

	cd ~/chef-repo
	bash bootstrap.sh cheftest cheftest
	

# build a crawler (cowapp)

at stage

	cd it/vm
	bash copy-vm.sh cowapp
	sudo su -
	echo 192.168.0.51 cowapp >> /etc/hosts
	exit
	bash it/gateway/reload-dnsmasq.sh
	sudo virsh autostart cowapp
	sudo virsh start cowapp --console
	# username ping, password 00000000

at cowapp

	sudo mv /etc/bash.bashrc.orig /etc/bash.bashrc
	echo cowapp | sudo tee /etc/hostname
	sudo sed -i s/basebox/cowapp/g /etc/hosts
	sudo sed -i s/192.168.0.199/192.168.0.51/g /etc/network/interfaces
	sudo reboot now
	ctrl + ]

at stage
	
	ssh -A chefbox

at chefbox

	ssh -A cowapp
	exit
	cd chef-repo
	gtpl
	bash bootstrap.sh cowapp cowapp
	bash addrl.sh cowapp 'role[crawler]'
	ssh -A cowapp

at cowapp

	sudo chef-client

at himalayas@cowapp

	for d in crawler-app scrapy .rc .vim bin
	do
		rsync -az front2:$d/ $d/
	done
	rsync -az front2:.ssh/config .ssh/
	ln -s .rc/gitconfig .gitconfig
	ln -s .rc/gitignore .gitignore
	ln -sf .rc/bashrc .bashrc
	ln -s .vim/vimrc .vimrc
	source .bashrc

	
# build chefserver

add a new interface to chefserver

	sudo virsh edit chefserver

add a new interface, exit

	<interface type='bridge'>
      <source bridge='br1'/>
      <model type='virtio'/>
    </interface>

stage 的网络配置如下

	$ cat /etc/network/interfaces
	auto lo
	iface lo inet loopback

	#auto eth1
	#iface eth1 inet manual
	auto br1
	iface br1 inet static
			address 60.28.194.156
			netmask 255.255.255.240
			gateway 60.28.194.145
			bridge_ports eth1
			bridge_fd 9
			bridge_hello 2
			bridge_maxage 12
			bridge_stp off

	auto eth0
	iface eth0 inet manual

	auto br0
	iface br0 inet static
			address 192.168.0.1
			netmask 255.255.255.0
			bridge_ports eth0
			bridge_fd 9
			bridge_hello 2
			bridge_maxage 12
			bridge_stp off

KVM supports 32-bit guests on 64-bit hosts, and any combination of PAE and non-PAE guests and hosts. The only unsupported combination is a 64-bit guest on a 32-bit host.

view the disk file

	mount -o loop,offset=32256 tmptqrug4 /mnt/vm


http://wiki.libvirt.org/page/Networking


	sudo apt-get install apt-proxy

vi /etc/apt-proxy/apt-proxy.conf 

	cache_dir = /data/cache/apt-proxy

	[ubuntu]
	backends =
		http://mirrors.sohu.com/ubuntu
		http://archive.ubuntu.com/ubuntu

	[ubuntu-security]
	backends =
		http://mirrors.sohu.com/ubuntu
		http://security.ubuntu.com/ubuntu

at stage

	*nat
	:PREROUTING ACCEPT [19189:1396308]
	:POSTROUTING ACCEPT [5110:330966]
	:OUTPUT ACCEPT [4073:277813]
	-A POSTROUTING -s 192.168.0.0/24 -o br1 -j SNAT --to-source 60.28.194.156
	COMMIT

at chefbox

	auto eth0
	iface eth0 inet static
			address 192.168.0.3
			netmask 255.255.255.0 
			gateway 192.168.0.1 
			# dns-* options are implemented by the resolvconf package, if installed
			dns-nameservers 192.168.0.1
			dns-search eee168.com


setup chef workstation at chefbox


# save and restore domain stage

	sudo virsh save webdev webdev.state

# manage img and snapshot

http://blog.allanglesit.com/2011/03/linux-kvm-managing-disk-images/

	kvm-img snapshot -c snapshot01 /kvm/images/disk/disk.img
	kvm-img snapshot -l /kvm/images/disk/disk.img
	kvm-img snapshot -a snapshot01 /kvm/images/disk/disk.img
	kvm-img snapshot -d snapshot01 /kvm/images/disk/disk.img

Create Layered Images (qcow2)

	# kvm-img create -f qcow2 /kvm/images/disk/base.img 20G
	# kvm-img create -f qcow2 -o backing_file=/kvm/images/disk/base.img /kvm/images/disk/disk.img
	# kvm-img info /kvm/images/disk/disk.img
	image: disk.img
	file format: qcow2
	virtual size: 20G (21474836480 bytes)
	disk size: 136K
	cluster_size: 65536
	backing file: /kvm/images/disk/base.img (actual path: /kvm/images/disk/base.img)

Commit Image Changes into Backing Image (qcow2)

	# kvm-img commit -f qcow2 /kvm/images/disk/disk.img
