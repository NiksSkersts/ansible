# ansible

How to start:
1. Create hosts.yml | either plain-text or using ansible-vault
2. `ansible-galaxy install -r requirements.yml`
3. `ansible-playbook playbooks/site.yml`

It should be easy to reverse engineer a host.yml file as most of the variables are available in the playbooks. All you need to do is to add your own secrets and hosts.