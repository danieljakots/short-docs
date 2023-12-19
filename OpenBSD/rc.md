## stuff related to rcscript

The rc.subr frameworks runs

~~~
# su -fl -c ${daemon_class} -s /bin/sh ${daemon_user} -c "${daemon} ${daemon_flags}"
# su -fl -c minio -s /bin/sh _minio -c "/usr/local/bin/minio server /var/minio/export"
~~~
