---
title: Reduce Duplicate Ansible Tasks with Jinja Conditional Logic
description: Learn how to use Jinja2 conditionals in Ansible to reduce duplicate tasks while keeping playbooks readable and maintainable.
summary: Use Jinja2 conditionals to reduce duplicate Ansible tasks without sacrificing readability.
date: 2023-12-28
lastmod: 2025-08-30
authors: Pat Martin
categories:
  - Quick Bytes
tags:
  - ansible
  - jinja
---

In general, we want to make our playbooks and tasks as simple as possible - but no simpler. 
Adding a little complexity at times can actually reduce our work and keep things readable.
Here's an example:

In the `microsoft.ad.group` Ansible module, there is an option to add users and a different option to remove users. 
That means you would have to write nearly identical tasks, one to add and one to remove. As shown below:

```yaml
---
- name: Add user from AD group
  hosts: all
  tasks:
    - name: Add members to the group, preserving existing membership
      microsoft.ad.group:
        name: "{{ groupname }}"
        scope: "{{ scope }}"
        members:
          add: "{{ username }}"
      when: user_option == 'add'

    - name: Remove members from the group, preserving existing membership
      microsoft.ad.group:
        name: "{{ groupname }}"
        scope: "{{ scope }}"
        members:
          remove: "{{ username }}"
      when: user_option == 'remove'
```

But there is an alternative; you could use one task with a Jinja if statement.

```yaml
- name: Add or Remove members from the group, preserving existing membership
  microsoft.ad.group:
    name: "{{ groupname }}"
    members:
      add: "{{ username if user_option == 'add' else omit }}"
      remove: "{{ username if user_option == 'remove' else omit }}"
```

Using Jinja templating like this should be used sparingly, but it reads well, and we don't have to repeat code almost word for word.
