# tower 
### starting from stratch - create repo
  mkdir Ansible-Tower
  cd Ansible-Tower
#  Ensure the collection is not stored in GIT Repo
#  cat > .gitignore <<EOF
#  collections/*
#  !collections/requirements.yml
#  EOF
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
  ## installs in HOMEDIR/.ansible - installs outside of REPO
  # ansible-galaxy collection install servicenow.servicenow 
  ## Better for tower - installs inside of REPO and static
  # cd /REPO
  # mkdir collections
  # ansible-galaxy collection install servicenow.servicenow -p collections/
  ## BEST for tower - CREATE collections/requirements.yml - Automatically run by TOWER
#  cat > collections/requirements.yml <<EOF
#  collections:
#  - name: servicenow.servicenow
#    source: https://galaxy.ansible.com
#  EOF
  # Servicenow Requires pysnow python module -- BETTER to create a yaml and install
  # Rather than do it manually - automation remember
  pip3 install pysnow
  pip3 list | grep pysnow
  # update ansible_cfg to path to plugin and FQCN i.e. /root/.ansible  - outside of REPO
  #vim /etc/ansible/ansible.cfg 
  # BEST Create a local ansible.cfg - in REPO - see ansible.cfg in this Repo
  vim /REPO/ansible.cfg
  # [ Default ]
  #
  #
  # [ Inventory ]
  # 
  #
### Use Servicenow plugin to query SNOW for CIs
  # Create now.yaml <-- name must end in now.yaml i.e. blah-now.yaml
  # see now.yaml for examples or
  # use ansible-doc to see the examples and create from that
  ansible-doc -t inventory servicenow.servicenow.now
  # following commands should now work
  # set variables instead of putting in now.yaml
  export SN_PASSWORD=XXX
  export SN_INSTANCE=dev80687
  export SN_USERNAME=admin
  ansible-inventory -i now.yaml --graph
  ansible-inventory -i now.yaml --list
  ansible-inventory -i now.yaml --host "OWA-SD-01"
### Create Incidents in Servicenow

