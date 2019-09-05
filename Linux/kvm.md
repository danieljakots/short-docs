## KVM install

### Install packages

~~~
# apt install qemu-kvm bridge-utils qemu-utils libvirt-daemon-system libvirt-clients virtinst
# kvm-ok # to check hw virt is enabled
~~~

### Configure network

Configure a bridge

~~~
# /etc/netplan/50-cloud-init.yaml should be
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
~~~

then apply

~~~
# netplan generate
# netplan --debug apply
# ifconfig # check br0
~~~

To delete virbr0

~~~
# virsh net-list
# virsh net-destroy default
# virsh net-undefine default
# service libvirtd restart
# ifconfig
~~~

## virt-install

~~~
virt-install --connect=qemu:///system \
  --name=VMNAME \
  --cpu mode=host-passthrough --vcpus=NCPU \
  --ram=MRAM \
  --disk path=/dev/ssd/VMNAME,bus=virtio,io=threads,cache=none,format=raw \
  --disk path=/home/vm/VMNAME.qcow2,bus=virtio,cache=none,size=35,format=qcow2 \
  --network=bridge:br0,model=virtio,mac=52:54:00:no:no:pe \
  --noautoconsole --graphics vnc,listen=127.0.0.1,keymap=en-us \
  --cdrom=/home/danj/install66.iso
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
      <boot dev='hd'/>
    </os>
[...]
    <disk type='file' device='cdrom'>
      <driver name='qemu' type='raw'/>
      <source file='/home/iso/install61.iso'/>
      <target dev='hda' bus='ide'/>
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
