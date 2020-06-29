## Initial setup

### create a bit of empty space at the end in case the next disk is slightly smaller

~~~
# # to check
# parted /dev/sdX 
mklabel gpt
mkpart primary 1048576B 500103643135B
p
name 1 thinkserver-luks
p
~~~

### create luks volume (luksFormat) and open them (luksOpen)

### create the zpool

~~~
# zpool create -o ashift=12 -O mountpoint=none -O compression=lz4 -O recordsize=1M -O atime=off -O exec=off -O setuid=off zpool0 mirror /dev/mapper/foo /dev/mapper/bar
# zpool list
# zpool status
# zpool import zpool0 # after reboot once the luks are opened
~~~

## datasets

~~~
# zfs create -omountpoint=/data/bleh zpool0/data/bleh
# apt install nfs-kernel-server
# zfs set sharenfs="ro=@198.51.100.12/32" zpool0/data/bleh
# zfs set sharenfs="no_root_squash,rw=@198.51.100.13/32" zpool0/data/whatever
# mount -t nfs -o nfsvers=3 198.51.100.14:/data/bleh /mnt
~~~

## maintainance

~~~
# zpool scrub zpool0
# zfs list -o space
~~~
