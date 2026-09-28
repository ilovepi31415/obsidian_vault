---
tags:
  - DevOps
---
[Topic 4 Link](https://gitlab.au-computing.org/andrews-university/courses/fall2026-cptr320/topics/topic-04-ansible-configuration-management)

Ansible is a way to standardize the config of different machines (often VMs) so you don't have to run the same commands over and over

## Ansible

The ansible configs are written in YAML, and playbooks often start from a `site.yml` (though this can be changed in the command)

In an `ansible/` folder (for convenience), the main things to add are:

`inventory.ini` - Lists the hosts that ansible manages
```c
fieldnotes-davidra.students.au-computing.org ansible_user=student
```

`ansible.cfg` - Sets flags for running the playbook, like a default inventory, turning off certain warning flags, etc.
```c
[defaults]
inventory = inventory.ini
```

A starting YAML file, often `site.yml` - where the `ansible-playbook` command targets
```yml
- name: Prepare the Fieldnotes host
  hosts: all
  become: true
  roles:
    - fieldnotes_host
```

### Roles

Roles are a way to manage the playbook in a standard and organized fashion. A basic file tree looks like this (taken from the `fieldnotes` project):
```yml
ansible/
├── ansible.cfg       
├── inventory.ini                  
├── site.yml         # top-level playbook: hosts + roles:            
└── roles/
    └── fieldnotes_host/   # or other role name
        │
        ├── defaults/
        │   └── main.yml   # role variables (lowest precedence)      
        │
        ├── tasks/
        │   └── main.yml    # THE ACTUAL WORK, run top to bottom:
        │                               
        ├── handlers/
        │   └── main.yml    # only runs when `notify:`d by a task    
        │
        ├── templates/      # rendered by the `template` module
        │   └── fieldnotes.service.j2  
        │   └── env.j2                  
        │
        └── files/          # copied VERBATIM by the `copy` module
            │               # (no templating — static file, as-is)
            └── nginx-fieldnotes.conf   
```

### Commands

```bash
# Syntax check only — parses YAML, validates task structure, no host contact
ansible-playbook site.yml --syntax-check

# Dry run — shows what WOULD change, without applying anything
ansible-playbook -i inventory.ini site.yml --check --diff

# Real run — actually applies changes
ansible-playbook -i inventory.ini site.yml

# Scope to one host (useful with multi-host inventories)
ansible-playbook -i inventory.ini site.yml --limit <hostname>

# Timed run — wraps the whole thing and reports real/user/sys time
time ansible-playbook site.yml
```

### Task Syntax

#### builtin.apt

Checks for status of `name:`'d packages using apt
```yaml
- name: Packages Ansible needs on this host
  ansible.builtin.apt:
    name:
      - acl
      - rsync
    state: present
    update_cache: true
    cache_valid_time: 3600
```

`state:` - `absent` or `present` for whether the package is wanted

#### builtin.file

Checks for files in the given `path`
```yaml
- name: The configuration directory exists
  ansible.builtin.file:
    path: /etc/fieldnotes
    state: directory
    owner: root
    group: root
    mode: "0755"
```

`state: directory` for folders, otherwise for files
`mode:` - sets permissions similar to `chown`

#### builtin.copy

Copies `content` to `dest`
```yaml
- name: SSH refuses passwords and root logins
  ansible.builtin.copy:
    content: |
      PasswordAuthentication no
      PermitRootLogin no
    dest: /etc/ssh/sshd_config.d/50-fieldnotes.conf
    owner: root
    group: root
    mode: "0644"
  notify: restart ssh
```

#### builtin.shell

Runs commands in the shell ¯\\\_(ツ)\_/¯
```yaml
- name: A JWT secret exists
  ansible.builtin.shell: umask 077 && echo "JWT_SECRET=$(openssl rand -hex 32)" > /etc/fieldnotes/jwt.env
  args:
    creates: /etc/fieldnotes/jwt.env
  notify: restart fieldnotes
```

Should use `args:` to stay idempotent
- `creates:` - checks for a file

`notify` - Runs the matching handler

#### builtin.service

Used to `reload` or `restart`
```yaml
- name: restart ssh
  ansible.builtin.service:
    name: ssh
    state: restarted
```

