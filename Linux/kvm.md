## KVM install

### Install packages

~~~
# apt install qemu-kvm bridge-utils qemu-utils libvirt-daemon-system libvirt-clients virtinst
# kvm-ok # to check hw virt is enabled
~~~

### Configure network

Configure a bridge

~~~
# /etc/netplan/00-installer-config.yaml should be
network:
  ethernets:
      eno1:
          dhcp4: false
  version: 2
  bridges:
    br0:
      interfaces: [eno1]
      mtu: 1500
      dhcp4: yes
      accept-ra: false
      addresses:
        - 2001:db8:b8e:10::30/64
      gateway6: 2001:db8:b8e:10::1
      nameservers:
        addresses: [2001:db8:b8e:10::1]
        search: [example.com]
~~~

then apply

~~~
# netplan generate
# netplan --debug apply
# ifconfig # check br0
~~~

To delete virbr0

~~~
# virsh net-list && virsh net-destroy default && virsh net-undefine default && service libvirtd restart
# ifconfig
~~~

## virt-install

~~~
virt-install --connect=qemu:///system \
  --name=VMNAME \
  --cpu mode=host-passthrough --vcpus=NCPU \
  --ram=MRAM \
  --disk path=/dev/zvol/zpool0/zvol/VMNAME,bus=virtio,io=threads,cache=none,format=raw \
  --disk path=/home/vm/VMNAME.qcow2,bus=virtio,cache=none,size=35,format=qcow2 \
  --network=bridge:br0,model=virtio,mac=52:54:00:no:no:pe \
  --noautoconsole --graphics vnc,listen=127.0.0.1,port=5901,keymap=en-us \
  --cdrom=/var/lib/libvirt/images/cd70.iso \
  --os-variant=ubuntu21.04
~~~

## Virsh commands

~~~
# virsh list --all
# virsh start VM
# virsh shutdown VM
# virsh destroy VM
# virsh edit VM # (file in /etc/libvirt/qemu/)
# virsh dominfo VM
# virsh autostart [--disable] VM
# virsh dumpxml VM > VM.xml && virsh define VM.xml
# virsh undefine VM
# virsh define /etc/libvirt/qemu/VM.xml
~~~

## Misc

### VNC connection

Get the vnc port

~~~
virsh qemu-monitor-command VM --hmp "info vnc"
~~~

Connect through the kvm server (requires ssnvc package)

~~~
$ vncviewer -via kvm 127.0.0.1:5903 # on OpenBSD
$ /usr/lib/ssvnc/vncviewer  -via kvm 127.0.0.1:5903 # on Ubuntu
~~~


### CD rom for installation

~~~
    <os>
      <type arch='x86_64' machine='pc-0.12'>hvm</type>
      <!-- add this line -->
      <boot dev='cdrom'/>
      <boot dev='hd'/>
    </os>
[...]
    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/var/lib/libvirt/images/install61.iso'/>
      <target dev='sda' bus='sata'/>
      <readonly/>
      <address type='drive' controller='0' bus='0' target='0' unit='0'/>
    </disk>
~~~

### qwerty layout

It's not just "us" but "en-us"

~~~
<graphics type='vnc' port='-1' autoport='yes' listen='127.0.0.1' keymap='en-us'>
~~~

### Copy qcow2 with rsync

~~~
$ rsync -SPv /home/vm/VM.qcow2 kvm2:/home/vm/ # initial copy
$ rsync -Pv --inplace /home/vm/testobsd.qcow2 kvm2:/home/vm/ # update copy
~~~

### Resize a disk

~~~
kvm# zfs get volsize zpool0/zvol/prom0
kvm# zfs set volsize=35G zpool0/zvol/prom0
vm# lsblk # check the disk size is good and/or halt -p ; uv start prom0
vm# parted /dev/vda # then p, resizepart, number, 100%, p
vm# pvdisplay
vm# pvresize /dev/vda3
vm# pvdisplay
vm# df -h
vm# lvextend -r -l+100%FREE  /dev/ubuntu-vg/ubuntu-lv
vm# df -h
~~~

### Create a qcow with a specific size

~~~
# qemu-img create -o cluster_size=8K -f qcow2 test0.qcow2 1G
~~~
