+++
title = "LACP 802.3ad bonding for Ubuntu hosts in WSL2"
date = "2025-07-01"
description = "A guide to getting LACP functional for Ubuntu hosts on Containerlab in Windows"
tags = [
    "bonding",
    "containerlab",
    "lacp",
    "wsl2",
    "linux",
    "ubuntu",
]
showComments = "true"
robots = "all"
+++

## Introduction

This article will show you how to get 802.3ad LACP bonding working easily on Linux hosts in Containerlab inside of a WSL2 instance, despite search results an your friendly LLM telling you this is not possible (unless you build your own WSL2 kernel). 

## Issue

If you have tried to enable bonding in the traditional way, you will see the below error message:

```bash
admin@host1:~$ sudo modprobe bonding mode=802.3ad miimon=100 lacp_rate=fast
modprobe: FATAL: Module bonding not found in directory /lib/modules/6.6.87.2-microsoft-standard-WSL2
```

Searching around you will find the below explanation:

> WSL2 does not support loading arbitrary kernel modules, including bonding, using modprobe by default. The WSL2 kernel is a custom kernel with pre-compiled modules and does not include the bonding module or support for loading new modules. To use bonding in WSL2, you would need to build a custom WSL2 kernel with the module included and then configure WSL2 to use that custom kernel. 

## Solution

To fix this, we can simply include the iproute2 package in the Dockerfile builds of your relevant container, i.e.

```Dockerfile
FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive

# Install iproute2
RUN apt-get update -y && apt-get install -y \
iproute2 \
&& apt-get autoremove -y \
&& apt-get clean -y \
&& rm -rf /var/lib/apt/lists/*
```

A full example host Dockerfile is available [here](https://github.com/commitconfirmed/npa-showcases/blob/main/containers/lab-host/Dockerfile)

{{< alert >}}
**Note**: I also build the iperf3 application from source since in this Dockerfile since the ubuntu package manager has an older version. You can remove this and the gcc / make packages if you don't want to use iperf3
{{< /alert >}}

From here, you can simply execute the below in a script on the host or via the Containerlab [exec](https://containerlab.dev/cmd/exec/) option in your clab file:

```bash
ip link add bond0 type bond
ip link set bond0 type bond mode 802.3ad lacp_active on lacp_rate fast
ip link set eth1 down
ip link set eth2 down
ip link set eth1 master bond0
ip link set eth2 master bond0
ip addr add 192.168.100.10/24 dev bond0
ip link set bond0 up
```

A full list of options can be seen by executing `ip link help bond`

<details>
<summary>Expand / Collapse command output</summary>

```bash
admin@host1:~$ ip link help bond
Usage: ... bond [ mode BONDMODE ] [ active_slave SLAVE_DEV ]
                [ clear_active_slave ] [ miimon MIIMON ]
                [ updelay UPDELAY ] [ downdelay DOWNDELAY ]
                [ peer_notify_delay DELAY ]
                [ use_carrier USE_CARRIER ]
                [ arp_interval ARP_INTERVAL ]
                [ arp_validate ARP_VALIDATE ]
                [ arp_all_targets ARP_ALL_TARGETS ]
                [ arp_ip_target [ ARP_IP_TARGET, ... ] ]
                [ ns_ip6_target [ NS_IP6_TARGET, ... ] ]
                [ primary SLAVE_DEV ]
                [ primary_reselect PRIMARY_RESELECT ]
                [ fail_over_mac FAIL_OVER_MAC ]
                [ xmit_hash_policy XMIT_HASH_POLICY ]
                [ resend_igmp RESEND_IGMP ]
                [ num_grat_arp|num_unsol_na NUM_GRAT_ARP|NUM_UNSOL_NA ]
                [ all_slaves_active ALL_SLAVES_ACTIVE ]
                [ min_links MIN_LINKS ]
                [ lp_interval LP_INTERVAL ]
                [ packets_per_slave PACKETS_PER_SLAVE ]
                [ tlb_dynamic_lb TLB_DYNAMIC_LB ]
                [ lacp_rate LACP_RATE ]
                [ lacp_active LACP_ACTIVE]
                [ ad_select AD_SELECT ]
                [ ad_user_port_key PORTKEY ]
                [ ad_actor_sys_prio SYSPRIO ]
                [ ad_actor_system LLADDR ]
                [ arp_missed_max MISSED_MAX ]

BONDMODE := balance-rr|active-backup|balance-xor|broadcast|802.3ad|balance-tlb|balance-alb
ARP_VALIDATE := none|active|backup|all|filter|filter_active|filter_backup
ARP_ALL_TARGETS := any|all
PRIMARY_RESELECT := always|better|failure
FAIL_OVER_MAC := none|active|follow
XMIT_HASH_POLICY := layer2|layer2+3|layer3+4|encap2+3|encap3+4|vlan+srcmac
LACP_ACTIVE := off|on
LACP_RATE := slow|fast
AD_SELECT := stable|bandwidth|count
```
</details>

## Demonstration

See the below output from a host in my [npa-showcases](/npa-showcases) repo in the [basic-ceos-clos](https://github.com/commitconfirmed/npa-showcases/tree/main/examples/basic-ceos-clos) lab.

```bash
admin@host1:~$ ip addr list bond0.100
9: bond0.100@bond0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether aa:c1:ab:44:7a:12 brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.10/24 scope global bond0.100
       valid_lft forever preferred_lft forever
    inet6 fe80::a8c1:abff:fe44:7a12/64 scope link
       valid_lft forever preferred_lft forever

admin@host1:~$ cat /proc/net/bonding/bond0
Ethernet Channel Bonding Driver: v6.6.87.2-microsoft-standard-WSL2

Bonding Mode: IEEE 802.3ad Dynamic link aggregation
Transmit Hash Policy: layer2 (0)
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
Peer Notification Delay (ms): 0

802.3ad info
LACP active: on
LACP rate: fast
Min links: 0
Aggregator selection policy (ad_select): stable

Slave Interface: eth1
MII Status: up
Speed: 10000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: aa:c1:ab:44:7a:12
Slave queue ID: 0
Aggregator ID: 2
Actor Churn State: monitoring
Partner Churn State: monitoring
Actor Churned Count: 0
Partner Churned Count: 0

Slave Interface: eth2
MII Status: up
Speed: 10000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: aa:c1:ab:9e:04:09
Slave queue ID: 0
Aggregator ID: 2
Actor Churn State: monitoring
Partner Churn State: monitoring
Actor Churned Count: 0
Partner Churned Count: 0

admin@host1:~$ ping 192.168.100.1
PING 192.168.100.1 (192.168.100.1) 56(84) bytes of data.
64 bytes from 192.168.100.1: icmp_seq=1 ttl=64 time=1.91 ms
64 bytes from 192.168.100.1: icmp_seq=2 ttl=64 time=1.24 ms
^C
--- 192.168.100.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.241/1.577/1.913/0.336 ms
```
