# luks

## Create container

~~~
# cryptsetup -v luksFormat /dev/vdb
~~~

## Decrypt container

~~~
# cryptsetup luksOpen /dev/bleh cryptname
~~~

## Resize container

~~~
# cryptsetup resize cryptname -v
~~~

## Check passphrase

~~~
# cryptsetup luksOpen --test-passphrase /dev/nvme0n1p3
~~~

## Close container

~~~
# cryptsetup luksClose cryptname
~~~
