+++
title = "A Basic introduction to Nornir (with lab!)"
date = "2025-05-17"
description = "Learn the Basics of Nornir with some hands on examples using containerlab"
tags = [
    "npa showcases",
    "containerlab",
    "nornir",
    "network automation",
    "python",
]
showComments = "true"
robots = "all"
+++

## Introduction

In this article, we will learn the basics of [Nornir](https://nornir.readthedocs.io/en/latest/) along with how to use it to interact with Juniper and Arista devices.

## What is Nornir?

From the Nornir [documentation](https://nornir.readthedocs.io/en/latest/)

> Nornir is an automation framework written in python to be used with python. Just imagine Nornir as the Flask of automation. Nornir will take care of dealing with the inventory where you have your host information, it will take care of dispatching the tasks to your devices and will provide a common framework to write “plugins”.

Where I think it shines compared to other products like Ansible is the "Pure Python" framework. If you can program in Python, then you can utilize Nornir to integrate with any product or API endpoint and create logic for any automation scenario.

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

## Interacting with Devices

Now the boilerplate is out of the way, let's jump right in! Assuming the manage.sh script worked, you should see the below output:

```sh
╭─────────────────┬───────────────────┬─────────┬────────────────╮
│       Name      │     Kind/Image    │  State  │ IPv4/6 Address │
├─────────────────┼───────────────────┼─────────┼────────────────┤
│ clab-lab-ceos1  │ arista_ceos       │ running │ 172.20.0.12    │
│                 │ ceos:latest       │         │ N/A            │
├─────────────────┼───────────────────┼─────────┼────────────────┤
│ clab-lab-crpd1  │ juniper_crpd      │ running │ 172.20.0.11    │
│                 │ crpd:latest       │         │ N/A            │
├─────────────────┼───────────────────┼─────────┼────────────────┤
│ clab-lab-nornir │ linux             │ running │ 172.20.0.100   │
│                 │ lab-nornir:latest │         │ N/A            │
╰─────────────────┴───────────────────┴─────────┴────────────────╯
Done. Sleeping for 5 seconds to allow the containers to fully boot
```
From here, SSH (admin/admin) into our Nornir server:

`ssh admin@clab-lab-nornir`

### Inventory

See the contents of the `/app/tasks/task-inventory.py` file below:

<details>
<summary>Expand / Collapse</summary>
{{< codeimporter url="https://raw.githubusercontent.com/commitconfirmed/npa-showcases/main/examples/basic-nornir/configs/lab-nornir/tasks/task-inventory.py" type="python" >}}
</details>

This script shows you how to: 

- Initialize a Nornir runner and your inventory
- Use the runner to execute a basic task
- Utilize the nornir_rich plugin to enhance your output

### NAPALM

See the contents of the `/app/tasks/task-napalm.py` file below:

<details>
<summary>Expand / Collapse</summary>
{{< codeimporter url="https://raw.githubusercontent.com/commitconfirmed/npa-showcases/main/examples/basic-nornir/configs/lab-nornir/tasks/task-napalm.py" type="python" >}}
</details>

This script shows you how to use [NAPALM](https://nornir.tech/nornir_napalm/html/) plugin to perform the below on EOS and JUNOS devices: 

- Retrieve the running configuration
- Execute a CLI command (show version)
- Render a Jinja2 configuration template and apply it

### NETMIKO

See the contents of the `/app/tasks/task-netmiko.py` file below:

<details>
<summary>Expand / Collapse</summary>
{{< codeimporter url="https://raw.githubusercontent.com/commitconfirmed/npa-showcases/main/examples/basic-nornir/configs/lab-nornir/tasks/task-netmiko.py" type="python" >}}
</details>

This script shows you how to use [NETMIKO](https://github.com/ktbyers/nornir_netmiko) plugin to perform the below on EOS and JUNOS devices: 

- Execute a CLI command (show version)
- Render a Jinja2 configuration template and apply it

### SCRAPLI

See the contents of the `/app/tasks/task-scrapli.py` file below:

<details>
<summary>Expand / Collapse</summary>
{{< codeimporter url="https://raw.githubusercontent.com/commitconfirmed/npa-showcases/main/examples/basic-nornir/configs/lab-nornir/tasks/task-scrapli.py" type="python" >}}
</details>

This script shows you how to use [SCRAPLI](https://scrapli.github.io/nornir_scrapli) plugin to perform the below on EOS and JUNOS devices: 

- Execute a CLI command (show version)
- Render a Jinja2 configuration template and apply it

{{< alert >}}
**Note:** The scrapli plugin requires a straight to CLI user (admin/clab123) on our cRPD device. This should be automatically applied but if not the commands are below
{{< /alert >}}

```sh
set system login user admin uid 100 class super-user
set system login user admin authentication plain-text-password
```

## Final thoughts

We have barely scratched the surface on what Nornir and its [plugins](https://nornir.readthedocs.io/en/latest/community/plugin_list.html) are capable of here but I hope this helps you get started with your Nornir journey. 

I've found that even in these basic examples, you can see that the code does vary a bit just to do the basics depending on your plugin of choice. A lot of basic documentation seems to be missing from some of the plugins as well, with the exception of the SCRAPLI plugin which I feel at moment is the best of the bunch, but YMMV.

Please let me know in the comments below if you have any thoughts, or if any of these scripts aren't working and I will update this article. Thanks for reading!



