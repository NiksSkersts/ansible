ansible-proxmox-server
=========

Does setup for a Proxmox server.

Requirements
------------

```
collections:

  - name: 'community.general'
    version: '*'
```

Role Variables
--------------

```
template: "https://cloud.debian.org/images/cloud/bookworm/20250115-1993/debian-12-generic-amd64-20250115-1993.tar.xz"
template_destination_path: /var/lib/vz/template/qemu
template_sha512: sha512:065a56184ecbddd583283ca854a274a17765d7ba1ff27cd34851eb9be9769c701e9db0e35c44480a080ff02ad2111ee1377a1e10458bcd63f2909a504c1a6d58
def_pool: 'rpool'

# Override these variables to authentificate with Proxmox API.
proxmox:
  api_user: root
  api_password: password
  api_host: localhost
  token_id: null
  api_token_secret: null

proxmox_defs:
  - zabbix:
    user: zabbix
    realm: pam
    userid: zabbix@pam
    acl:
      - path: /
        role: PVEAuditor
      - path: /storage
        role: PVEAuditor
      - path: /vms
        role: PVEAuditor

token_defs:
  - token0:
    secret: EXAMPLESECRET
    userid: zabbix@pam
    acl:
      - path: /
        role: PVEAuditor
      - path: /storage
        role: PVEAuditor
      - path: /vms
        role: PVEAuditor

lxc_defs: []

vm_defs:
  - vm_id: 100
    name: debian
    cpu_cores: 2
    vm_memory: 1024
    dns: 1.1.1.1
    diskn:
      - id: 1
        disk_key: scsi0
        disk_backup: "true"
        disk_discard: "on"
        disk_dest_pool: '{{ def_pool }}'
        disk_size: 32G
    netn:
      - id: 0
        bridge: vmbr0
        ip: 192.168.1.2
        gw: 192.168.1.1
        subnet: 24
        tag: 1111

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
```

Dependencies
------------

None, yet.


Example Playbook
----------------

TODO

License
-------

MIT

Author Information
------------------

Niks Skersts

Email: mail@nikssk.id.lv
