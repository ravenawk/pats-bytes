---
title: "Simplify Playbooks with module_defaults"
date: 2026-04-25
draft: false
summary: "Stop repeating connection params across tasks — module_defaults lets you set them once per play or block. Here's how and when to use it."
description: "Stop repeating connection params across tasks — module_defaults lets you set them once per play or block. Here's how and when to use it."
tags:
  - ansible
  - yaml
  - automation
cover:
  image: "cover.png"
  alt: "Before and after YAML showing module_defaults usage"
  relative: true
ShowToc: true
---

If you've ever written a playbook where every task repeats the same `become`, 
`no_log`, or module arguments, `module_defaults` is your fix.

## The Problem

{{< highlight yaml "linenos=true" >}}
tasks:
  - name: Create user alice
    ansible.builtin.user:
      name: alice
      state: present
      shell: /bin/bash

  - name: Create user bob
    ansible.builtin.user:
      name: bob
      state: present
      shell: /bin/bash
{{< /highlight >}}

`state: present` and `shell: /bin/bash` are repeated on every task. 
In a real playbook this gets tedious fast.

## The Fix

```yaml
- hosts: all
  module_defaults:
    ansible.builtin.user:
      state: present
      shell: /bin/bash
  tasks:
    - name: Create user alice
      ansible.builtin.user:
        name: alice

    - name: Create user bob
      ansible.builtin.user:
        name: bob
```

Much cleaner. Any value set in `module_defaults` becomes the default 
for every matching task in that play.

## Scope

You can also scope defaults to a `block` rather than the whole play — 
useful when only a subset of tasks share common args:

```yaml
tasks:
  - block:
      module_defaults:
        ansible.builtin.user:
          state: present
          shell: /bin/bash
      tasks:
        - ansible.builtin.user:
            name: alice
```

## Things to Know

- Task-level arguments **always win** over `module_defaults` — you can still override per task
- Use the fully qualified collection name (FQCN) like `ansible.builtin.user`, not just `user`
- Works with custom collections too: `myorg.mycollection.mymodule`
