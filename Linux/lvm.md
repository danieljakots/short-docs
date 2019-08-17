## Physical Volumes

~~~
# pvcreate /dev/sda1
# pvdisplay
~~~

## Volume Groups

~~~
# vgcreate VG_NAME /dev/sda1
# vgdisplay
~~~

## Logical Volumes

~~~
# lvcreate -L50G -nVM_NAME VG_NAME
# lvdisplay
# lvremove -v /dev/VG_NAME/VM_NAME
# lvrename VG_NAME OLD_NAME NEW_NAME
# lvextend -r -L+10G  /dev/VG_NAME/VM_NAME
# lvextend -r -l+100%FREE  /dev/VG_NAME/VM_NAME
~~~


