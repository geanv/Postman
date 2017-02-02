## Welcome to Postman Project

Postman is a distributed service aming to improve system performance in the network layer for small packets
by offloading packet processing.

### Introduction

Network layer usually performs poorly when processing small packets, because there is a constant overhead
associated with processing every packet. For example, for each packet, Linux needs to handle the interrupt
(and the associated context switch) caused by the packet, perform protocol operations, such as checksum
verification and congestion control, and process related system calls (also the associated context switch)
made by applications. As a result, when transferring 64-byte packets on 10Gb ethernet, Linux can only
deliver a throughput of 0.5Gb/s.

Postman deployes a number of helper nodes, which are equipped
with new techniques for packet processing (e.g., DPDK), to assemble small packets to the same server,
so that a server application can
enjoy both the high performance of processing large packets and the convenience of working on a classic
operating system.

### Postman Design

Postman aims to achieve the following targets:

- Efficient packet processing.  
A helper node needs to assemble small packets efficiently. For efficiency,
Postman implements a helper node under the popular Data Plane Development Kit (DPDK) framework.
To further improve performance, Postman 1) removes redundant information in packets’ headers when
assembling them, so that a server application does not need to pay the bandwidth cost for headers; 2)
strikes a balance between latency and throughput by dynamically
comparing the additional delay introduced by packets assembly and the reduced queueing delay.

- Transparent offloading.  
To make Postman practical, packet redirection, assembly, and disassembly should
be transparent to applications. To achieve that, we build a virtual connection library (VCL),
providing an illusion to a client that it is still talking to the server directly. VCL provides the same
interface as classic TCP libraries, but internally, a client will re-direct packets to helper nodes, and a
server will disassemble large packets from helper nodes. Furthermore, to tolerate helper node failures,
VCL incorporates a connection re-establishment protocol, guaranteeing that packets will not be lost, reordered,
or duplicated, despite helper node failures.

- Latency-aware helper placement and load balancing.  
Postman manages a helper as an instance of Virtual
Network Function (VNF) and use the recent Software Defined Networking (SDN) and Network Functions
Virtualization (NFV) techniques to design an automatic framework to place helpers in different locations
of the network, such that network management is simplified and network latency is minimized
