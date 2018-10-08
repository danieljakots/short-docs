## sudo

~~~
# visudo -cf /etc/sudoers
~~~

## OpenSSH

~~~
# sshd -T -f /etc/ssh/sshd_config
~~~

## OpenSMTPD


~~~
# smtpd -n

~~~

## Postfix

~~~
# postconf >/dev/null

~~~

## Bind

~~~
# named-checkzone domain /path/to/zone
# named-checkconf /etc/bind/named.conf

~~~

## NSD

~~~
# nsd-checkconf /var/nsd/etc/nsd.conf

~~~

## Unbound

~~~
# unbound-checkconf /var/unbound/etc/unbound.conf

~~~

## Apache

~~~
# apachectl -t
~~~

## Haproxy

~~~
# haproxy -c -f /etc/haproxy/haproxy.cfg

~~~

## httpd

~~~
# httpd -dnv

~~~

## Nginx

~~~
# nginx -t

~~~

## Varnish

~~~
# varnishd -Cf /etc/varnish/default.vcl > /dev/null

~~~

## Dovecot

~~~
# doveconf -n > /dev/null
~~~

## OpenBGPD

~~~
# bgpd -nf /etc/bgpd.conf
~~~
