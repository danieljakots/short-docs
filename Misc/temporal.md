## create namespace and change the retention

~~~
$ temporal operator namespace create --namespace infra --address 10.10.10.42:7233
$ temporal operator namespace list --address 10.10.10.42:7233
$ temporal operator namespace update --retention 21d --namespace dead-man-cycling --address 10.10.10.42:7233
~~~
