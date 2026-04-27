---
date: 2023-12-28
lastmod: 2025-08-30
categories:
  - quick-bytes
  - ansible-automation
tags:
  - ansible
  - jinja
authors:
  - pat
---
# Reduce Duplicate Ansible Tasks with Jinja Conditional Logic

In general we want to make our playbooks and tasks as simple as possible, but no simpler. 
But there are times that adding a little complexity actually reduces our work and is still easy to read.
Let me share an example:

In the `microsoft.ad.group` Ansible module, there is an option to add users and a different option to remove users. 
That means you would have to write nearly identical tasks, one to add and one to remove. As shown below:

<!-- more -->
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
