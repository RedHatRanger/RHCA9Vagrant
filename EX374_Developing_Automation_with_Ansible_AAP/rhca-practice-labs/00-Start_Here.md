## Register as a Red Hat Developer Here FIRST:
```

```

## Sign in and download the [Ansible Automation Platform Setup Bundle](https://developers.redhat.com/content-gateway/file/ansible/Ansible_Automation_Platform_2.4/ansible-automation-platform-setup-bundle-2.4-1-x86_64.tar.gz target=_blank)

## From the Windows Host Machine, ssh to the Ansible control node as the `rhel` user:
```PowerShell
ssh rhel@172.28.128.100
```

## Register Your Ansible Control Node:
```bash
sudo subscription-manager --register
# Enter your developer username and password
sudo subscription-manager auto-attach
```

## Yum install `ansible`, `ansible-core`, `python3-pip`, `container-tools`, and `rhel-system-roles`:
```
sudo yum install -y ansible ansible-core python3-pip container-tools rhel-system-roles
```

## Yum update the system and reboot:
```
sudo yum -y update
sudo init 6
```

## Generate the ~/.bashrc file for shortcut access to the nodes:
```bash
mkdir ansible
echo "alias go='ssh -Xq -o ServerAliveInterval=60'" >> ~/.bashrc
echo "alias node1='go rhel@node1'" >> ~/.bashrc
echo "alias node2='go rhel@node2'" >> ~/.bashrc
echo "alias node3='go rhel@node3'" >> ~/.bashrc
echo "alias gitlab='go rhel@gitlab'" >> ~/.bashrc
echo "alias nav='ansible-navigator run -m stdout'" >> ~/.bashrc
echo "cd /home/rhel/ansible" >> ~/.bashrc
source ~/.bashrc
```

## Configure the ~/.vimrc file:
```bash
cat << EOF > /home/rhel/.vimrc
autocmd FileType yaml setlocal ai ts=2 sw=2 et
EOF
```

## Generate the ansible.cfg file
```bash
cat << EOF > /home/rhel/ansible/ansible.cfg
[defaults]
remote_user=rhel
inventory=/home/rhel/ansible/inventory
roles_path=/home/rhel/ansible/roles:/etc/ansible/roles
collections_path=/home/rhel/ansible/collections:/etc/ansible/collections
ask_pass=false
host_key_checking=false
callbacks_enabled=profile_tasks
forks=10

[privilege_escalation]
become=true
become_medthod=sudo
become_user=root
become_ask_pass=false
EOF
```

## Setup the important Ansible directories:
```bash
mkdir -p /home/rhel/ansible/{roles,collections,playbooks,templates}
```

## Build the Inventory:
```bash
cat << EOF > /home/rhel/ansible/inventory
[nodes]
node1
node2
node3

[gitlab]
gitlab
EOF
```

## Setup the `host_vars`:
```bash
mkdir -p /home/rhel/ansible/host_vars
cat << EOF > /home/rhel/ansible/host_vars/node1.yml
ansible_host: 172.28.128.101
ansible_user: rhel
EOF

cat << EOF > /home/rhel/ansible/host_vars/node2.yml
ansible_host: 172.28.128.102
ansible_user: rhel
EOF

cat << EOF > /home/rhel/ansible/host_vars/node3.yml
ansible_host: 172.28.128.103
ansible_user: rhel
EOF

cat << EOF > /home/rhel/ansible/host_vars/gitlab.yml
ansible_host: 172.28.128.104
ansible_user: rhel
EOF
```

## Create a "defaults.yml" file inside the "group_vars" folder:
```bash
cat << EOF > group_vars/defaults.yml
---
ansible_user: rhel
ansible_python_interpreter: /usr/bin/python3
EOF
```

## Install the `ansible.posix` and `community.general` collections:
```bash
ansible-galaxy collection install https://galaxy.ansible.com/download/ansible-posix-2.0.0.tar.gz -p /home/rhel/ansible/collections/
ansible-galaxy collection install https://galaxy.ansible.com/download/community-general-10.1.0.tar.gz -p /home/rhel/ansible/collections/
```

## Install `ansible-navigator` using python3-pip:
```
pip3 install ansible-navigator
```

## Pass the control node's public ssh key to the nodes:
```bash
for i in {node1,node2,node3,gitlab}; do ssh-copy-id -i /home/rhel/.ssh/id_rsa.pub $i; done
```

## Use Ansible-Navigator to kick off the ssh_keys.yml file (IN YOUR ~/.BASHRC, nav="ansible-navigator run -m stdout"):
```bash
nav /home/rhel/ansible/playbooks/ssh_keys.yml

# The first time you run ansible-navigator, it will take a few moments because it is downloading the podman container
```
