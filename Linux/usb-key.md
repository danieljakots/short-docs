# Encrypt a usb key and format it

## Initial setup

~~~
$ lsblk
$ sudo cryptsetup -v luksFormat DEVICE
$ sudo cryptsetup luksOpen /dev/sdb cryptname
$ ls -l /dev/mapper/cryptname
$ sudo mkfs.ext4 /dev/mapper/cryptname
$ sudo mount /dev/mapper/cryptname /mnt/
~~~

## umount

~~~
$ sudo umount /mnt
$ sudo cryptsetup luksClose cryptname
~~~

## remount

~~~
$ sudo cryptsetup luksOpen /dev/sdb cryptname
$ sudo mount /dev/mapper/cryptname /mnt/
~~~
