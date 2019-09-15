# PostgreSQL

## ~/.pgpass format (remember to chmod 600)

~~~
hostname:port:database:username:password
~~~

## Create user with a password

~~~
$ createuser -P USERNAME
~~~

## Create a database while setting a user as owner

~~~
$ createdb -O USERNAME DATABASE
~~~

## Delete a database


~~~
$ dropdb DATABASE
~~~

## Delete a user

~~~
$ dropuser USERNAME
~~~

## List users

~~~
=# \du
~~~

## List databases

~~~
$ psql -l
~~~

~~~
=# \l
~~~

with details (size, tablespace, description)

~~~
=# \l+
~~~

## List tables

~~~
DB=# \d
~~~

with details (size and description)

~~~
DB=# \d+
~~~

## Print the schema for TABLE (aka *describe TABLE* in MySQL land)

~~~
DB=# \d+ TABLE
~~~

## Show/hide the request timer

~~~
=# \timing
~~~

## Change table ownership

~~~
=# ALTER TABLE table OWNER TO user;
~~~
