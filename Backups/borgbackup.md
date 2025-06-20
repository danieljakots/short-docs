## Link to upstream's documentation

<https://borgbackup.readthedocs.io/en/stable/>

## Set the env

~~~
$ cd /home/duplin/borg/.zfs/snapshot/zfs2-weekly-2025-06-02T00:00Z # don't tr(y|ust)ls(1)
$ export BORG_REPO=/path/to/repo
$ export BORG_PASSPHRASE=hunter2
~~~

## List available archives

~~~
$ borg list
$ borg list --bypass-lock
~~~

##  List the content of one archive

~~~
$ borg list ::hostname-2019-02-07T05:10:02
~~~

## Restore the content of /etc/ssl

"Currently, extract always writes into the current working directory (“.”), so
make sure you cd to the right place before calling borg extract."

~~~
$ cd /home/user/restore/dest
# remove the --dry-run once it's checked
$ borg extract --list --dry-run ::hostname-2019-02-07T05:10:02 etc/ssl
# to remove "etc/ssl" from the path of the file it will create
$ borg extract --list --strip-components 2 --dry-run ::hostname-2019-02-07T05:10:02 etc/ssl
~~~

## Remove some backups

To clear some space

~~~
$ borg delete ::hostname-2019-04-21T28:10:02
~~~
