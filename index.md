## Welcome to Postman Project

PostMan is a distributed service to efficiently execute stateless operations.
It identifies application-specific stateless operations, such as query parsing and data compression,
as well as a generic stateless operation—processing
network packets. These stateless operations can account for
up to 90% of applications’ execution time and thus is worth
offloading.

### Introduction

Networking layer usually performs poorly when processing
small packets, because there is a constant overhead associated
with processing each packet. For example, when transferring
packets using the TCP protocol, the IP header of each
packet takes 20 bytes and the TCP header takes another 20
bytes: these additional bytes not only consume bandwidth,
but also incur CPU overhead. As a result, when transferring
64-bytes packets on 10Gb ethernet (CPU: Intel Xeon CPU E5-2650 v3, NIC: Intel X520), Linux can only deliver a throughput of ~ 910 MB/s ( or ~ 890K requests/s).

Postman can offload the packet processing
to other nodes that have free cycles: these helper nodes can
assemble small packets into big ones and send the big packet
to the helper, so that the helpee can enjoy the benefits of processing
large packets. Our idea is motivated by a key observation
that, in today’s datacenters, a few nodes that are processing
small packets are becoming scalability bottlenecks.

### Postman Design

PostMan: the core of PostMan is a number
of execution nodes that perform stateless operations for applications:
application developers need to submit a piece of
code (App logic) to execution nodes to describe how to perform
stateless operations. Application client and server communicate
with these execution nodes through given libraries,
which guarantee that, despite failures, requests and replies
are not lost, not duplicated, and not re-ordered.
