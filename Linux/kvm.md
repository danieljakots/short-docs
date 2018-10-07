## virt-install

~~~
virt-install --connect=qemu:///system \
  --name=VMNAME \
  --cpu mode=host-passthrough --vcpus=NCPU \
  --ram=MRAM \
  --disk path=/dev/ssd/abitibi,bus=virtio,io=threads,cache=none,format=raw \
  --network=bridge:br0,model=virtio \
  --noautoconsole --graphics vnc,listen=127.0.0.1,keymap=en-us \
  --cdrom=/home/danj/install63.iso

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

### Get VNC port

~~~
virsh qemu-monitor-command VM --hmp "info vnc"
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
