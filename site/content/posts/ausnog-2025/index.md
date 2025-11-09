+++
title = "AUSNOG 2025 Notes"
date = "2025-05-17"
description = "Just some general notes from attending AUSNOG 2025"
tags = [
    "ausnog",
    "network automation",
    "python",
]
showComments = "true"
robots = "all"
draft = "true"
+++

## Nexthop

- Strategies to ping everything in/out of our network from generic monitoring and routers themselves
- Arista allows you to run containers within their routers/switches as its just an AlmaLinux base
- Can also just runs scripts on the switch itself
- Running a python daemon that connects to kakfa
- Arista comes with ProcMgr inbuilt to monitor your custom script/process

## Reannz

Network State Checking 

- perfSONAR
- config creation, then validation during migration using an inbuilt tool

# AWS

No Packet Left Behind

- Network owned end to end
- 96% automation due to scale (1 million network devices)
- Single chip routers, fixed ports
- No fabric, cellification, dual REs, state sync, etc.
- 32 x 400G 12.8TB switch (can breakout to 100G)
- Deployed 1 rack at a time in a clos fabric, pre-cabled
- Auto remediation stages
  - Detect/Isolate
  - Identify Root Case
  - Mitigate impact
  - Remediate underlying problem
  - Return to Service
- Vendor discard counters vary per vendor, even platform in the same vendor
  - No standards 
  - draft-evans-discardclass
- Shift / Drain / etc.
  - Take device out of service (as path, is-is pref, etc.)
  - ECMP allows to take out single link
  - Rollback no matter the change, can always roll forward again
- Deployment
  - Change -> Validate -> Intended State -> Deployment System -> Applied state -> Observed State
  - Batfish for Correctness of Intent
  - Containerlab / NetLab / etc. for actual implementation testing
- At scale
  - 2500 lines of instructions to an LLM agent
  - This is just tribal knowledge

## Nokia

- 8 x GPU collective operations (RCCL, NCCL, etc.)
- Dataset with Weights / Gradients 
- All-reduce, all-gather for distributed parallel flows
- All of these have a large impact on the network
  - 3 Phases (Sync, Compute, Communicate)
  - Periodic bursts of large traffic (elephant flow problem)
- Rail network is used to build a GPU -> GPU topology (ultra ethernet)
  - Consider Intra and Inter GPU server comms
  - Inter GPU uses RoCEv2
- Physical (Modular AI clusters)
  - SU (Scalable unit) aka Stripe
  - Line rate across the whole cluster
  - 32 x GPU Servers (other configs available)
  - 3 x Mgmt TOR
  - 2 spine 3 leaf
- Logical Networks
  - Interconnect
  - In-band
  - Out-of-band
  - Backend GPU
  - Storage
- Considerations
  - RDMA primary traffic 
  - Lossless RoCEv2 -> UEC
- Getting to lossless ethernet
  - ECN + ECN Bits
  - PFC (microbursts) 802.1Qbb
    - Pause frames
- Storage
  - Checkpointing to compensate for GPU failures
  - Each SU has a dedicated storage fabric (clos)
  - NVMe-oF

## Nodal/ys

How geopolitics is rerouting submarine cables

- Hyperscalers beginning to owning cables outright
- US/China 
  - PLCN, BtoBE, MEA-ME-WE6, SJC2

# Cloudflare

Beyond the Firewall

- Sarah Armstong-smith books
  - Human hacking
  - Social Engineering
- https://radar.cloudflare.com
- No blame culture
- Move beyond generic annual training, focus on the why and make it relevant
- Know your business, communicate with employees
  - What are they doing and need to do (no workarounds/shadow IT)
- Project Zero Trust - George Finney
- Flipper

## Kentik

The Scourge of Excessive AS-Sets

- AS-SET in IRR
- bgp4q to build BGP filter lists on AS-SETs
- Check the authoritative source (APNIC/RIPE/etc.) of the AS-SET in PeeringDB
- When making your AS-SET
  - Remove recursive AS-SETs where possible
  - Otherwise keep to minimum
- Long term solution
  - In-band BGP signaling (RFC 9234)
  - RPKI-based signaling using ASPA verification
  - Future RPKI extensions

## BGP Tools

How far can you go with IX route servers only?

- IX Route servers
  - Solves the problem of reaching out to individuals for peering at IXPs
  - Generally far safer than bi-lat peering
- Cumulative IX peers diminishing returns
- map.bgp.tools
- CGNAT /24's generally don't respond to pings but accounts for lots of traffic

## 5G Networks

BFD going down, from BGP timers expiring?

- JTAC Bug and process of case

## Telair

A smaller NBN rollout

- NBN NNI attached to EVPN PWE
- Terminated on Juniper BNG


# Day 2

## Nokia

Quantum Technology

- Qubits / Superposition / Entanglement
- 4 Qubit word is 2^4 (all possible permutations)
- HNDL Attacks post Q-Day (RSA-2048 cracked in 24h)
  - Harvest data now, decrypt later
- Symmetric Crypto in use today, either PSK or asymmetric Key Sharing
  - Move to AES256 & SHA512 for safety
- Public Key Cryptography
  - Larger keys, one way Algos (RSA, DH, etc.)
  - Algos use large 1 way prime factors, reversing this is compute heavy
  - Shor's Algorithm
  - QKD may be used in future to share these keys
  - Otherwise PQC coming up with new Algorithm for key exchange
  - Mosca's theorem

## Leaptel

Mikrotiks doing CGNAT

- ~40Gb

## Juniper

Evolving Broadband Design in Australia

- Cloudified BGP network (Spine/Leaf for access + BNG for scaleout)
  - ISIS SRv4
  - EVPN-VPWS + ESI
- BNG CUPS
  - DBNG-MP (Mgmt)
  - DBNG-CP (Ctrl)
  - DBNG-UP (User)
  - Allows for Local resiliency and Geo Redundancy designs
- IPv6, QoS, Merchant, Automation

## Cisco

Transport Protocols Evolution

- MPLE TE Challenges
  - RSVP hard to state (large headend / midpoint)
  - Core device stats k*n^2
- SR/SRv6
  - State is in packet
  - Eliminates LDP/RSVP
  - No Tunnel interfaces
  - Multi-domain with PCE/BSID
  - SRv6 goes back to OSI model due to IPv6 headers
- 80-90% engineers used RSVP TE For FRR
- Achieved in SR with Ti-LFA
- Flex Algo (native steering of traffic of FA path)
  - Can use Metric / Constraints
- On Demand Nexthop
  - HE automatically creates SR policy to BGP NH
  - Uses SR-PCE (when different HE and TE)
  - Can do per-flow

## APNIC Labs

Evolution of TCP Transport Protocols

- Speed evolving, plateaus recently
- Optical Transmission outpacing TCP speed
- Reno, increase conservatively but not as suitable these days for Gbit networks
- Ramp up based on MSS / RTT, drop 50% on loss
  - Would take 3 years on a Tbit link
- Cubic used on modern Linux
  - Non linear algorithim
  - Reacts quickly to capacity in network
- Buffer bloat causes delay loops (queue never drains)
- Small buffers also bad (can't utilize link)
- BW * RTT / sqrt(N)
- Turn on Pacing on servers
- ECN (network should hit hte point of just beginning to buffer)
- ECN not in use
- TCP BBR tries to accomodate for this

## Telstra

Breaking the light barrier: Optical spectrum sharing

- WDM expensive to deploy
- Providers now offering spectrum with users owning their transponders 
- Services (Wavelength / Spectrum sharing / Dark Fibre)

## AARNet

AARNet Network Architecture

- Automation
  - Model Driven approach (services, lifecycle, etc.)
  - Cisco NSO implementation
    - Northbound RESTCONF API
    - Multivendor, flexible (orchestrator or device can be master)
  - Service defined as YANG
  - YANG model then used to build device config template

## Vocus

Defending Telco Networks

- Essentials
  - Separate networks
  - No Generic credentials
  - Harden
  - MFA, complex passwords that are rotated
  - CLI audits
  - Jumphosts (MFA)
  - Zero Trust

## Arista

Comfortable Complexity of Overlays

- EVPN single service plane
  - Protocol reduction
  - Repeatable Model
  - Flexible multi-homing
  - Any Encapsulation
  - Converged teams
- EVPN Gateway to stitch transport domains (i.e. dc to wan to campus)
  - RFC 9014
  - Type-5 used and readvertised using GW next-hop
    