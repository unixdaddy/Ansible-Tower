# tower 
### starting from stratch - create repo  
mkdir -p Ansible-Tower/collections  
cd Ansible-Tower  
  
\##Ensure the collection is not stored in GIT Repo  
cat > .gitignore <<EOF  
collections/*  
!collections/requirements.yml  
EOF  
  git init  
  git status  
  git add .  
  git status  
  git commit -m "Testing using collections in Ansible Tower"  
  git branch  
  git branch -M Main  
  git branch  
  git remote add origin git@github.com:USERNAME/REPO.git  
  git push -u origin main  
  git remote  
  git remote -v  
### Existing Repo with setup already in it
  cd   
  git clone git@github.com:USERNAME/REPO.git
  cd Ansible-Tower/
### Install Servicenow Collection
  \##installs in HOMEDIR/.ansible - installs outside of REPO  
   ansible-galaxy collection install servicenow.servicenow   
   \##Better for tower - installs inside of REPO and static  
   cd /REPO  
   mkdir collections  
   ansible-galaxy collection install servicenow.servicenow -p collections/  
  \##BEST for tower - CREATE collections/requirements.yml - Automatically run by TOWER  
cat > collections/requirements.yml <<EOF  
collections:  
\- name: servicenow.servicenow  
   source: https://galaxy.ansible.com  
EOF  
   ansible-galaxy collection install -r collections/requirements.yml -p collections/  
  \## Servicenow Requires pysnow python module -- BETTER to create a yaml and install  
  \## Rather than do it manually - automation remember  
  pip3 install pysnow  
  pip3 list | grep pysnow  
  \## update ansible_cfg to path to plugin and FQCN i.e. /root/.ansible  - outside of REPO  
  vim /etc/ansible/ansible.cfg   
  \## BEST Create a local ansible.cfg - in REPO - see ansible.cfg in this Repo  
  \## copy /etc/ansible/ansible.cfg into REPO and amend
  vim /REPO/ansible.cfg  
  [defaults]  
  
  inventory_plugins  = /usr/share/ansible/plugins/inventory:./collections  
  collections_paths = ./collections:/usr/share/ansible/collections  
  
  [inventory]   
  enable_plugins = host_list, virtualbox, yaml, ini, constructed, servicenow.servicenow.now  
      
### Use Servicenow plugin to query SNOW for CIs - now.yaml
  \## Create now.yaml <-- name must end in now.yaml i.e. blah-now.yaml  
  \## see now.yaml for example OR  
  \## use ansible-doc to see the examples and create from that  
  ansible-doc -t inventory servicenow.servicenow.now  
  \## following commands should now work  
  \## set variables instead of putting in now.yaml  
  export SN_PASSWORD=XXX  
  export SN_INSTANCE=devXXXXX  
  export SN_USERNAME=admin  
  ansible-inventory -i now.yaml --graph  
  ansible-inventory -i now.yaml --list  
  ansible-inventory -i now.yaml --host "OWA-SD-01"  
### Create Incidents in Servicenow - snow_incident.yaml
  \## Create yaml file  
  \## see snow_incident.yaml for example OR  
  \## use ansible-doc to see the examp;les and create from that  
  ansible-doc servicenow.servicenow.snow_record  
  \## You can either use ENV or Create a vault encrypted file with credentials i.e password.yaml  
  \## In the password file set SN_PASSWORD SN_INSTANCE SN_USERNAME  
  \## Then load it in your playbook i.e snow_incident.yaml  
  \## if not using ENV  
   ansible-playbook snow_incident.yaml --ask-vault-pass  
  \## if using ENV (comment out load password task in snow_incident.yaml  
  export SN_PASSWORD=XXX  
  export SN_INSTANCE=devXXXXX  
  export SN_USERNAME=admin  
  ansible-playbook snow_incident.yaml  
### Find Incidents in Servicenow - snow_incident_find.yaml  
  \## Create yaml file  
  \## see snow_incident_find.yaml for example OR  
  \## use ansible-doc to see the examp;les and create from that  
  ansible-doc servicenow.servicenow.snow_record_find  
  \## You can either use ENV or Create a vault encrypted file with credentials i.e password.yaml  
  \## In the password file set SN_PASSWORD SN_INSTANCE SN_USERNAME  
  \## Then load it in your playbook i.e snow_incident_find.yaml  
  \## if not using ENV  
   ansible-playbook snow_incident_find.yaml --ask-vault-pass  
  \## if using ENV (comment out load password task in snow_incident_find.yaml  
  export SN_PASSWORD=XXX  
  export SN_INSTANCE=devXXXXX  
  export SN_USERNAME=admin  
  ansible-playbook snow_incident_find.yaml  
### Install Webservers on Digital Ocean VMs - webservers.yaml  
  \## After creating VM using doctl  
  \#doctl compute droplet create --region lon1 --image ubuntu-18-04-x64 --size s-1vcpu-1gb ansible-1 --ssh-keys ID  
  \## Create Using inventory using do-ansible-inventory  
  \#do-ansible-inventory --group-by-tag --group-by-project --group-by-region --out ~/do_inventory/inventory  
  ansible-playbook -i ../do_inventory/inventory webservers.yaml -e 'host_name=all' --ask-vault-pass  
### Install Webservers on Digital Ocean VMs and Generate a incident - webservers_snow_incident.yaml
  \## This will deliberatly trigger an incident when things start  
  \## Normally you would trigger it on a failure but i am testing  
  ansible-playbook -i ../do_inventory/inventory webservers_snow_incident.yaml -e 'host_name=all' --ask-vault-pass  
