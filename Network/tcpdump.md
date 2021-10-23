## TCPDUMP

### quiet icmp6

~~~
ICMPv6 type are:

    unreachable: 1
    too-big: 2
    time-exceeded: 3
    echo-request: 128
    echo-reply: 129
    router-solicitation: 133
    router-advertisement: 134
    neighbor-solicitation: 135
    neighbor-advertisement: 136
# tcpdump -ni vlan20 "icmp6 && ip6[40] != 135 && ip6[40] != 136"
~~~
