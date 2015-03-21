# Ansible Cloudstack Routers

Manage your CloudStack routers with Ansible.

# Usage

## 1. Fork this repo and git clone your fork on your CloudStack management server

    git clone git@github.com:<your_handle>/ansible-cloudstack-routers.git
    cd ansible-cloudstack-routers

## 2. Install Ansible on your CloudStack management server

    pip install ansible
    pip install -r requirements.txt

## 3. Create a .cloudstack.ini in your $HOME

Admin keys:

~~~
cat $HOME/.cloudstack.ini
[cloudstack]
endpoint = https://cloud.example.com/client/api
key = cloudstack api key
secret = cloudstack api secret 
~~~

## 4. Run the playbook

Limit on backup routers in a HA VR setup in dry-run:

    ansible-playbook playbooks/site.yml --limit redundant_backup_routers --check

Limit per CloudStack projects:

    ansible-playbook playbooks/site.yml --limit <project>

## 5. Support my work, buy me some beer.

 * Flattr: https://flattr.com/profile/resmo
 * BTC: https://www.coinbase.com/resmo
 * Paybal: mail@renemoser.net

# Website

The origin of the dynamic inventory for routers has its home at https://github.com/resmo/ansible-cloudstack. Please track this repo for updates
