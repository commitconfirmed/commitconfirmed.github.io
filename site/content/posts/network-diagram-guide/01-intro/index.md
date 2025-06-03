+++
title = "Network Diagram Guide - Intro"
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
series_order = 1
showComments = "true"
draft = "true"
+++

## Introduction

In this series, I will go through some personal experiences, tools and recommendations when drawing Network Diagrams. 

This article deals with the tools I have used to document, plan & draw diagrams. I will also go over where each tool shines and tips & tricks to make your life easier. Again, this is based on personal experience since there are a lot of free and paid tools out there.

## Excel

Excel as a Network Diagram tool? I have surprisingly seen topology diagrams in excel before but generally it is used to draw up Elevation/Rack diagrams and data center floor plans.



Let's start with the bane of every Network Engineers existence, Excel. We have all had to deal with an IP address spreadsheet sometime in our lives. 

It is very easy to spin up a basic network source of truth these days. 

Where Excel is does shine is its speed

## Visio


## draw.io


## Excalidraw


## other

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
