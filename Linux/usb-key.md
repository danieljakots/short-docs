# Encrypt a usb key and format it (with lvm)

## Initial setup

~~~
$ lsblk
$ sudo cryptsetup -v luksFormat DEVICE
$ sudo cryptsetup luksOpen DEVICE cryptname
$ sudo pvcreate /dev/mapper/cryptname
$ sudo vgcreate VGNAME /dev/mapper/cryptname
$ sudo lvcreate -L2G -n LVNAME VGNAME
$ ls -l /dev/mapper/cryptname
$ sudo mkfs.ext4 /dev/mapper/cryptname
$ sudo mount /dev/mapper/cryptname /mnt/
~~~

## umount

~~~
$ sudo umount /mnt
$ sudo vgchange -a n VGNAME
$ sudo cryptsetup luksClose cryptname
~~~

## remount

~~~
$ sudo cryptsetup luksOpen /dev/sdb cryptname
$ sudo mount /dev/mapper/cryptname /mnt/
~~~
