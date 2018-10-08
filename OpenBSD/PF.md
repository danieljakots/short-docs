## tcpdumping pflog

~~~
# tcpdump -n -e -ttt -i pflog0 # realtime
# tcpdump -n -e -ttt -r /var/log/pflog # from the last log file 
# zcat /var/log/pflog.0.gz |tcpdump -ne -ttt -r - # from a previous log file
~~~

## Killing states

~~~
# pfctl -ss -vv # get the ID
# pfctl -k id -k meh123 # kill the ID
# # kill all UDP states
# for _ID in $(pfctl -vvss | grep -A2 'all udp' | grep id | awk '{print $2}') ; do echo "${_ID}" ; pfctl -k id -k "${_ID}" ; done
~~~
