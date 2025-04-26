ansible-role-docker
=========

Deploy Docker containers using Ansible.

Requirements
------------

collections:

  - name: 'community.docker'
    version: '*'

Role Variables
--------------

EXAMPLE: 
```
wipe: false
docker_packages:
  - "docker-{{ docker_edition }}"
  - "docker-{{ docker_edition }}-cli"
  - "docker-{{ docker_edition }}-rootless-extras"
docker_edition: 'ce'
docker_packages_state: present
docker_users:
  - zabbix  # Add zabbix user to allow Zabbix agent2 to monitor it.
docker_defs:

  - example:
    name: pihole
    state: present
    domain: "dns1.example.com"
    rp_path: 127.0.0.1:8086
    definition:
      services:
        pihole:
          container_name: pihole
          image: pihole/pihole:latest
          ports:
            # DNS Ports
            - "53:53/tcp"
            - "53:53/udp"
            # Default HTTP Port
            - "80:80/tcp"
            # Default HTTPs Port. FTL will generate a self-signed certificate
            - "443:443/tcp"
            # Uncomment the below if using Pi-hole as your DHCP Server
            #- "67:67/udp"
          environment:
            # Set the appropriate timezone for your location (https://en.wikipedia.org/wiki/List_of_tz_database_time_zones), e.g:
            TZ: Etc/UTC
            # Set a password to access the web interface. Not setting one will result in a random password being assigned
            FTLCONF_webserver_api_password: SUPER+STRONG+PASS
            # If using Docker's default `bridge` network setting the dns listening mode should be set to 'all'
            FTLCONF_dns_listeningMode: 'all'
            FTLCONF_dns_upstreams: '1.1.1.1'
            FTLCONF_dns_hosts: '192.168.1.1 one.example.com;192.168.1.2 two.example.com;'
            FTLCONF_dns_domain: "dns1.example.com"
            FTLCONF_dns_cnameRecords: 'three.example.com,one.example.com;four.example.com,two.example.com;'
          # Volumes store your data between container upgrades
          volumes:
            # For persisting Pi-hole's databases and common configuration file
            - '/opt/pihole:/etc/pihole'
            # Uncomment the below if you have custom dnsmasq config files that you want to persist. Not needed for most starting fresh with Pi-hole v6. If you're upgrading from v5 you and have used this directory before, you should keep it enabled for the first v6 container start to allow for a complete migration. It can be removed afterwards. Needs environment variable FTLCONF_misc_etc_dnsmasq_d: 'true'
            #- './etc-dnsmasq.d:/etc/dnsmasq.d'
          cap_add:
            # See https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
            # Required if you are using Pi-hole as your DHCP server, else not needed
            - NET_ADMIN
            # Required if you are using Pi-hole as your NTP client to be able to set the host's system time
            - SYS_TIME
            # Optional, if Pi-hole should get some more processing time
            - SYS_NICE
          restart: unless-stopped
```

Dependencies
------------

```
roles:
  - name: 'geerlingguy.docker'
```
Quick note, role will be written to be fully independent.

Example Playbook
----------------

```
```

License
-------

MIT

Author Information
------------------

Niks Skersts

Email: mail@nikssk.id.lv

