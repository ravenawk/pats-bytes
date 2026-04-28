---
title: "How to Create a Template with HashiCorp Packer on Proxmox VE"
description: Learn how to create a RHEL template on Proxmox VE using HashiCorp Packer. A step-by-step guide to building consistent, reusable VM images. 
summary: Use HashiCorp Packer to automate the creation of a reusable RHEL template on Proxmox VE.
date: 2023-09-30
authors: Pat Martin
tags: 
 - packer
categories: 
  - Quick Start
---
Manually setting up the same VM again and again gets old fast. HashiCorp Packer fixes this by automating the creation of reusable, templated “gold images”. 
In this post, we’ll walk through how to build a template on Proxmox VE.

I've recently been learning Packer, and here I'll share an example of using Packer to generate a Red Hat Enterprise Linux (RHEL) template on Proxmox. 
While I'm still exploring the intricacies of Packer, this article will remain at a fairly high level. I hope it gives you a solid starting point.


## Prerequisites:

- A Proxmox environment set up and running
- A RHEL ISO (we will use RHEL 8 in this article)
- Packer installed on your local machine (see references for installation docs)

## Step 1: Create the Packer Configuration File

Configuration file named `rhel-template.pkr.hcl`:

{{< highlight hcl "linenos=true" >}}
packer {
  required_plugins {
    proxmox = {
      version = ">= 1.1.3"
      source  = "github.com/hashicorp/proxmox"
    }
  }
}

variable "proxmox_host" {
    type = string
}

variable "proxmox_node" {
    type = string
}

variable "iso_file" {
    type = string
}

variable "proxmox_user" {
    type = string
}

variable "proxmox_pass" {
    type = string
}

variable "vm_name" {
    type = string
}

variable "ssh_user" {
    type = string
}

variable "ssh_pass" {
    type = string
}

source "proxmox-iso" "rhel-tpl" {

    proxmox_url = "https://${var.proxmox_host}/api2/json"
    insecure_skip_tls_verify = true
    node = var.proxmox_node
    iso_file = var.iso_file
    vm_name = var.vm_name
    username = var.proxmox_user
    password = var.proxmox_pass
    cpu_type = "Nehalem"
    cores = "2"
    memory = "1024"
    disks {
        disk_size         = "60G"
        storage_pool      = "local-lvm"
        type              = "virtio"
    }
    network_adapters {
        bridge = "vmbr0"
    }
    ssh_username = var.ssh_user
    ssh_password = var.ssh_pass
    ssh_timeout = "30m"
    ssh_handshake_attempts = "100"
    boot_command = ["<esc><wait>", "vmlinuz initrd=initrd.img ", "inst.ks=http://{{ .HTTPIP }}:{{ .HTTPPort }}/ks.cfg", "<enter>"]
    http_directory = "http"
}

build {
    sources = ["source.proxmox-iso.rhel-tpl"]
}
{{< /highlight >}}

There are four parts of the above config that I want to call out.

- The top section (lines 1-8) is how we load the plugin for proxmox
- The following section (lines 10-40) defines the Packer variables. It doesn't assign them; it just defines them—more on assigning later
- In the next section (lines 42-68), defines what Packer needs for connection and setting up the template on proxmox. Things like proxmox url, iso file to use, CPU/memory, disk, etc., are all assigned here
- The last section (lines 70-72) is the build section, where you can add provisioners to customize your template further—things like running shell scripts, Ansible playbooks, or other configuration management tools after the OS installation completes. For this walkthrough, I'm skipping additional provisioning and letting the template build with just the kickstart configuration

## Step 2: Install the Packer Proxmox Plugin

With the above configuration file we can run:

```bash
packer init .
```

The `packer init .` command will install the Packer Proxmox plugin.

## Step 3: Set Up a Kickstart Configuration

The configuration file's boot_command above references a Kickstart file (`ks.cfg`). 
Create this in a directory named `http`:

```bash
mkdir http
```

Here's a basic `ks.cfg` to get started:

```cfg
#version=RHEL8
lang en_US.UTF-8
keyboard us
timezone America/Los_Angeles --isUtc
rootpw your-rhel-root-password
reboot
text
cdrom
bootloader --append="rhgb quiet crashkernel=1G-4G:192M,4G-64G:256M,64G-:512M"
zerombr
clearpart --all --initlabel
autopart
skipx
firstboot --disable
selinux --enforcing
%packages
@^minimal-environment
%end
```
Replace the values with `your-` with your actual values.

## Step 4: Create a vars file
This file will assign values to the variables you define in the first configuration file. 
Here is an example:

## rhel_vars.pkrvars.hcl

```hcl
proxmox_host = your-proxmox-host
proxmox_node = "pve01"
iso_file = "isos:iso/rhel-8.6-x86_64-dvd.iso"
proxmox_user = your-promox-user
proxmox_pass = your-proxmox-user-password
ssh_user = "root"
ssh_pass = your-rhel-root-password
vm_name = "rhel-tpl"
```

Replace the values that start with "your-" with your values.

### Step 5: Run Packer

With configurations set, initiate the Packer build:

```bash
packer build -var-file=rhel_vars.pkrvars.hcl .
```

Replace the values with `your-` with your actual values.
Monitor Packer's output. It will create the VM, provision it, and convert it into a Proxmox template.

Once Packer completes, log into the Proxmox web interface. You should see the new RHEL template. Clone this template whenever you need a new RHEL 8 instance.

---

With Packer, you can automate the creation of standardized machine images for Proxmox, ensuring consistent, repeatable deployments across your infrastructure.

{{< admonition type="tip" title="Living Document" >}}
This article is as much for my own reference as it is a tutorial for others.
There are items I didn't touch on and I know that.
I'll continue updating it as I discover new Packer techniques and refinements.
{{< /admonition >}}

## References and Further Reading

- [Packer](https://www.packer.io/)
- [Packer Install Docs](https://developer.hashicorp.com/packer/tutorials/docker-get-started/get-started-install-cli)
- [Proxmox](https://www.proxmox.com/en/)
- [Developer Subscription for RHEL](https://developers.redhat.com/articles/faqs-no-cost-red-hat-enterprise-linux)
