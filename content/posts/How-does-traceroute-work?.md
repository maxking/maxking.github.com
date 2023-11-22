---
title: "How does traceroute work?"
date: "2017-08-06 09:40:53"
type: "post"
---


# How does traceroute work?
`traceroute` is a tool that people often use to determine the path of a packet as it starts from your machine to your destination server. On Ubuntu systems you can install it using the following command:


    $ sudo apt install traceroute

Then you can use it to trace the route back to let’s say this server (asynchronous.in) :


    $ traceroute asynchronous.in
    traceroute to asynchronous.in (104.24.120.93), 30 hops max, 60 byte packets
     1  10.249.0.2 (10.249.0.2)  2.242 ms  2.087 ms  2.239 ms
     2  mc2-...oregonstate.edu (128.193.106.132)  2.557 ms  2.515 ms  3.150 ms
     3  mc2-HUB.cr.net.oregonstate.edu (128.193.106.130)  2.556 ms  2.519 ms  2.763 ms
     4  ob1.oregonstate.edu (128.193.88.50)  2.983 ms  3.261 ms  3.102 ms
     5  ob2.oregonstate.edu (128.193.88.66)  3.313 ms  2.930 ms  2.889 ms
     6  50.232.212.105 (50.232.212.105)  3.192 ms  4.157 ms  3.883 ms
     7  ae-50-....or.bverton.comcast.net (68.87.222.133)  24.748 ms  24.611 ms  24.551 ms
     8  be-...1.seattle.wa.ibone.comcast.net (68.86.92.217)  12.829 ms  14.872 ms  12.749 ms
     9  be-...2.seattle.wa.ibone.comcast.net (68.86.86.226)  10.011 ms  10.142 ms  11.963 ms
    10  66.208.229.58 (66.208.229.58)  9.444 ms  9.401 ms  9.712 ms
    11  104.24.120.93 (104.24.120.93)  9.831 ms  10.114 ms  11.886 ms

Wow! That sounds like fun! But how does it even work?

So, the implementation of traceroute in GNU/Linux uses [ICMP protocol](https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol). It is a Layer 2 protocol and is also used in other network tools like `ping` for example. ICMP protocol is typically used for network debugging and don’t really carry forward any data. Any error in the path of the packet is relayed back to the source IP address.

ICMP protocol has this header called as TTL (Time To Live), also called as *max no. of hops* that a packet can go through before reaching it’s destination. If you look at the above traceroute response, you will notice that is has a `30 hops max`. So, everytime an intermediate router receives an ICMP packet, it decrements the TTL value by 1. If it results in TTL=0, the packet is dropped and an ICMP error message is relayed back to the source IP Address.

Traceroute uses this property to trace the path of a packet. It first sends a packet with TTL = 1, so at it’s first hop, the value of TTL is decreased to 0 causing the packet to be dropped and an ICMP error message it relayed back to the source IP. Then, a 2nd packet with TTL = 2 is sent and it gets dropped at the 2nd hop and an ICMP error message is relayed back from the point of it’s 2nd hop. So on and so forth at least N numbers of packets are sent untill an `ICMP Echo Reply` is received from the destination, where N is the number of hops between source and destination hosts.

It is worthy to note that the packets don’t always take the same route, so it is possible to have different response each time you run traceroute. It is possible that the Host at a certain hop didn’t reply within a certain time limit, in which case the host is skipped in the output.


