+++
title = "Network Diagram Guide - Physical"
date = "2025-06-02"
description = "A guide to drawing better Network Diagrams"
tags = [
    "network engineering",
    "network diagrams",
    "drawio",
    "visio",
    "excalidraw",
]
series = [
    "Network Diagram Guides"
]
series_order = 2
showComments = "true"
draft = "true"
+++

## Introduction

In this series, I will go through some personal experiences, tools and recommendations when drawing Network Diagrams. 

This article deals with the Physical side of Network Engineering (Datacenters, Network Hardware, Cabling & Schematics). 



## Setup

The ready to go example lab for this article is the [basic-nornir](https://github.com/commitconfirmed/npa-showcases/tree/main/examples/basic-nornir) topology. Navigate into this directory and execute `manage.sh build` & `manage.sh run` 

{{< alert >}}
**Note:** If this is your first time here, see the [NPA Showcases](/npa-showcases) documentation for initial installation steps on your local machine or a GitHub codespace
{{< /alert >}}

## Topology

The folder structure of our Nornir server is below

| File | Description | 
| --- | --- |
| `/app/config.yml` | Our basic Nornir config file | 
| `/app/inventory/defaults.yml` | Default inventory variables, applies to all hosts | 
| `/app/inventory/groups.yml` | Group inventory variables, applies to hosts in this group | 
| `/app/inventory/hosts.yml` | Host inventory variables, specific to the host | 
| `/app/tasks/task-inventory.py` | Example task showing inventory management |
| `/app/tasks/task-napalm.py` | Example task to interact with devices using NAPALM plugin |
| `/app/tasks/task-netmiko.py` | Example task to interact with devices using NETMIKO plugin |
| `/app/tasks/task-scrapli.py` | Example task to interact with devices using SCRAPLI plugin |
| `/app/templates/ceos1.j2` | Jinja2 template for our cEOS device |
| `/app/templates/crpd1.j2` | Jinja2 template for our cRPD device |
