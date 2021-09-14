# luks

## Create container

~~~
# cryptsetup -v luksFormat /dev/vdb
~~~

## Decrypt container

~~~
# cryptsetup luksOpen /dev/bleh cryptnane
~~~

## Resize container

~~~
# cryptsetup resize cryptname -v
~~~
