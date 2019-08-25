## Docker

~~~
$ docker stats --no-stream # info about resources used
$ docker tag foo:v0 reg.example.com/foo:v0 && docker push reg.example.com/foo:v0 # publish an image to a registry
$ docker system df
$ docker system prune
$ docker system prune -a # clean really everything
~~~

## docker-compose

~~~
$ docker-compose ps --services # get the name known by docker-compose
~~~
