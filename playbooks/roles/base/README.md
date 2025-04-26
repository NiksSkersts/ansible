ansible-role-vm
=========

Setup VM using Ansible

Requirements
------------

```
collections:

  - name: 'ansible.posix'
    version: '*'
```

Role Variables
--------------

EXAMPLE:

```
user_defs:
 - user1:
   user: user1
   group: user1
   groups:
     - sudo
   home: true
   password: HASHEDPASSWORD
   state: present
   shell: /bin/bash
   ssh_pub: "{{ ssh.pub }}"
   system: false

pkgs:
  - neovim
  - dnsutils
  - net-tools
  - cronutils
  - systemd-cron
  - curl
  - tree
  - htop
  - ncdu
  - qemu-guest-agent
```

Dependencies
------------

None, yet.

Example Playbook
----------------


```

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

- name: EXAMPLE playbook
  hosts: localhost
  gather_facts: true
  roles:
    - role: ansible-role-vm
      user_defs:
       - user1:
         user: user1
         group: user1
         groups:
           - sudo
         home: true
         password: HASHEDPASSWORD
         state: present
         shell: /bin/bash
         ssh_pub: "{{ ssh.pub }}"
         system: false
      pkgs:
        - neovim
        - dnsutils
        - net-tools
        - cronutils
        - systemd-cron
        - curl
        - tree
        - htop
        - ncdu
        - qemu-guest-agent
```

License
-------

MIT

Author Information
------------------

Niks Skersts

Email: mail@nikssk.id.lv
