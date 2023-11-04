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
# zfs set sharenfs="rw=@192.0.2.122/32,rw=@203.0.113.121/32" zpool1/data/blah
# zfs set sharenfs="no_root_squash,rw=@198.51.100.13/32" zpool0/data/whatever
# mount -t nfs -o nfsvers=3 198.51.100.14:/data/bleh /mnt
~~~

## maintainance

~~~
# zpool scrub zpool0
# zfs list -o space
~~~

## Resize a pool

~~~
# zpool online -e zpool1 vdb
# zpool list -v # check resize
~~~

## zvol

~~~
# zfs create -V 50G zpool0/zvol1
# zfs set volsize=75G zpool0/zvol1
~~~

## send/receive

~~~
# zfs snapshot zpool0/zvol/bleh@backup
# zfs send zpool0/zvol/bleh@backup | ssh zfs1 "zfs recv zpool1/zvol/bleh"
# zfs snapshot zpool0/zvol/bleh@backup2
# zfs send -I zpool0/zvol/bleh@backup zpool0/zvol/bleh@backup2 | ssh zfs1 "zfs recv zpool1/zvol/bleh"
~~~

### resumable send/receive

~~~
sender# zfs send -v zpool1/data/jeancanard@eldindono | ssh -4 receiver "zfs recv -v -s zpool0/zfs1/data/jeancanard"
receiver# zfs get receive_resume_token zpool0/zfs1/data/jeancanard
sender# zfs send -v -t SUPERLONGTOKEN | pv --quiet --rate-limit 1M | ssh -4 receiver "zfs recv -v -s zpool0/zfs1/data/jeancanard"
~~~


### create a test pool

~~~
$ for i in {0..3} ; do truncate -s 1G $i.raw ; done
$ ll
total 6.2M
-rw-rw-r-- 1 danj danj 1.0G Nov  4 11:28 0.raw
-rw-rw-r-- 1 danj danj 1.0G Nov  4 11:28 1.raw
-rw-rw-r-- 1 danj danj 1.0G Nov  4 11:28 2.raw
-rw-rw-r-- 1 danj danj 1.0G Nov  4 11:28 3.raw
# zpool create -o ashift=12 -O mountpoint=none -O compression=lz4 -O recordsize=1M -O atime=off -O exec=off -O setuid=off zpool0 mirror /tmp/zfs.MxhW/0.raw /tmp/zfs.MxhW/1.raw mirror /tmp/zfs.MxhW/2.raw /tmp/zfs.MxhW/3.raw
# zpool status
  pool: zpool0
 state: ONLINE
config:

	NAME                     STATE     READ WRITE CKSUM
	zpool0                   ONLINE       0     0     0
	  mirror-0               ONLINE       0     0     0
	    /tmp/zfs.MxhW/0.raw  ONLINE       0     0     0
	    /tmp/zfs.MxhW/1.raw  ONLINE       0     0     0
	  mirror-1               ONLINE       0     0     0
	    /tmp/zfs.MxhW/2.raw  ONLINE       0     0     0
	    /tmp/zfs.MxhW/3.raw  ONLINE       0     0     0

errors: No known data errors

~~~
