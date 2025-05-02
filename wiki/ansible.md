```
---
# tasks file for vm-role-nfs-server
# https://github.com/geerlingguy/ansible-role-nfs/tree/master

- name: Create system user if not exists
  ansible.builtin.user:
    name: "{{ item.user }}"
    password: "{{ common.admin_password | default(omit) }}"
    state: present


---

- name: Add Samba users
  block:

    - name: Check if user name {{ item.user }} exists 
      ansible.builtin.command: "id {{ item.user }}"
      register: user_check
      ignore_errors: true

    - name: Create system user if not exists
      ansible.builtin.user:
        name: "{{ item.user }}"
        password: "{{ common.admin_password | default(omit) | password_hash }}"
        state: present
      when: user_check.rc != 0

    - name: Add Samba user to smbpasswd if not exists
      ansible.builtin.command: "pdbedit -L -u {{ item.user }}"
      register: smb_check
      ignore_errors: true

    - name: Set Samba password if user not exists
      ansible.builtin.command: 
        cmd: "smbpasswd -s -a {{ item.user }}"
        stdin: |
          {{ common.admin_password }}
          {{ common.admin_password }}
      when: smb_check.rc != 0

```