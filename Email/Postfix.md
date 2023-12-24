# Postfix

## Show the email queue

~~~
# mailq
~~~

or

~~~
# postqueue -p
~~~

## Flush the mail queue

~~~
# postfix -f
~~~

## Remove an email from the mail queue

~~~
# postsuper -d 8BC82528CE
~~~

## Check config

~~~
# postconf | grep whateversetting
# postconf > /dev/null
~~~
