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


## create a test pool

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

## Replace a faulty disk (ZABCDE4)

`halt -p`, plug it physically and power on

~~~
# zpool status
  pool: zpool1
 state: ONLINE
  scan: scrub repaired 0B in 06:16:30 with 0 errors on Fri Nov  3 07:51:59 2023
config:

	NAME          STATE     READ WRITE CKSUM
	zpool1        ONLINE       0     0     0
	  mirror-0    ONLINE       0     0     0
	    ZABCDE1  ONLINE       0     0     0
	    ZABCDE2  ONLINE       0     0     0
	  mirror-1    ONLINE       0     0     0
	    ZABCDE3  ONLINE       0     0     0
	    ZABCDE4  ONLINE       0     0     0

errors: No known data errors
# cryptsetup luksOpen /dev/disk/by-id/ata-ST4000VN008-2DR166_ZDH9WXYZ ZDH9WXYZ
# zpool attach zpool1 /dev/mapper/ZABCDE3 /dev/mapper/ZABCDE5
invalid vdev specification
use '-f' to override the following errors:
/dev/mapper/ZABCDE5 is part of potentially active pool 'zpool0'
# zpool attach -f zpool1 /dev/mapper/ZABCDE3 /dev/mapper/ZABCDE5
# zpool status
  pool: zpool1
 state: ONLINE
status: One or more devices is currently being resilvered.  The pool will
	continue to function, possibly in a degraded state.
action: Wait for the resilver to complete.
  scan: resilver in progress since Sat Nov  4 15:54:04 2023
	31.1G scanned at 3.89G/s, 15.3G issued at 1.91G/s, 5.33T total
	0B resilvered, 0.28% done, 00:47:32 to go
config:

	NAME          STATE     READ WRITE CKSUM
	zpool1        ONLINE       0     0     0
	  mirror-0    ONLINE       0     0     0
	    ZABCDE1  ONLINE       0     0     0
	    ZABCDE2  ONLINE       0     0     0
	  mirror-1    ONLINE       0     0     0
	    ZABCDE3  ONLINE       0     0     0
	    ZABCDE4  ONLINE       0     0     0
	    ZABCDE5  ONLINE       0     0     0

errors: No known data errors
# zpool status
  pool: zpool1
 state: ONLINE
  scan: resilvered 2.68T in 06:45:10 with 0 errors on Sat Nov  4 22:39:14 2023
config:

	NAME          STATE     READ WRITE CKSUM
	zpool1        ONLINE       0     0     0
	  mirror-0    ONLINE       0     0     0
	    ZABCDE1  ONLINE       0     0     0
	    ZABCDE2  ONLINE       0     0     0
	  mirror-1    ONLINE       0     0     0
	    ZABCDE3  ONLINE       0     0     0
	    ZABCDE4  ONLINE       0     0     0
	    ZABCDE5  ONLINE       0     0     0

errors: No known data errors
# zpool detach zpool1 ZABCDE4
# zpool status
  pool: zpool1
 state: ONLINE
  scan: scrub repaired 0B in 06:37:46 with 0 errors on Sun Nov  5 05:17:56 2023
config:

	NAME          STATE     READ WRITE CKSUM
	zpool1        ONLINE       0     0     0
	  mirror-0    ONLINE       0     0     0
	    ZABCDE1  ONLINE       0     0     0
	    ZABCDE2  ONLINE       0     0     0
	  mirror-1    ONLINE       0     0     0
	    ZABCDE3  ONLINE       0     0     0
	    ZABCDE5  ONLINE       0     0     0

errors: No known data errors
~~~
