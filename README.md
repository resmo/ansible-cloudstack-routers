# Ansible Cloudstack Routers

Manage your CloudStack routers with Ansible (XEN, VMware).

## Summary

Install this repo on a linux workstation which can SSH in to the cloudstack manamgent server. Ansible is configured to use the cloudstack managment server as a SSH jump host to SSH into the routers.


# Usage

## 1. Fork this repo 

git clone your on a linux server having SSH access to the cloudstack managmement server.

    git clone git@github.com:<your_handle>/ansible-cloudstack-routers.git
    cd ansible-cloudstack-routers

## 2. Install Ansible and dependencies

    pip install ansible
    pip install -r requirements.txt

## 3. Configure access to the CloudStack API

Create a cloudstack.ini in the working dir or a ~.cloudstack.ini in your $HOME having the admin keys:

~~~
cat $HOME/.cloudstack.ini
[cloudstack]
# admin keys!
endpoint = https://cloud.example.com/client/api
key = cloudstack api key
secret = cloudstack api secret 
~~~

Verify the access to the cloud:

~~~
cs listRouters
~~~

Verify the dynamic inventory also returns a json

~~~
./hosts/cloudstack-routers.py --list
~~~

## 4. Modify the ssh.config

This is necessery for jump host configuration.

~~~
cp ssh.config.origin ssh.config
# Replace cloud.example.com with your cloudstack managment server fqdn
sed -i 's/cloud\.example\.com/<cloudstack_fqdn>/g' ssh.config

# Modify ssh.config if routers are using an IP not in the network range 10.0.0.0/8 e.g. for 192.169.0.0/16
sed -i 's/10\.\*/192\.168\.\*/g' ssh.config

# NOTE: I didn't find a way to use the private key from the jumphost. 
# Copy the private ssh key from the cloudstack managment server locally 
scp root@<cloudstack_fqdn>:/var/lib/cloud/management/.ssh/id_rsa ~/.ssh/<cloudstack_fqdn>_id_rsa
chmod 0600 ~/.ssh/<cloudstack_fqdn>_id_rsa
~~~

Verify the access (use a `linklocalip` from the command `cs listRouters`):

~~~
ssh -F ssh.config 10.x.y.z
~~~

## 5. Test Ansibe access

Get the output of the command uptime of all routers

~~~
ansible -a "uptime" all
~~~

Play a bit! Routers are dynamiclly grouped by project, account, domain, zone, non_redundant_routers, redundant_routers, redundant_backup_routers, redundant_master_routers. This allows to filter by groups. 

~~~
ansible -a "uptime" <zone_name>
~~~

## 6. Run the playbook

Limit on backup routers in a HA VR setup in dry-run:

    ansible-playbook playbooks/site.yml --limit redundant_backup_routers --check

Limit router for a project:

    ansible-playbook playbooks/site.yml --limit <project>
   
