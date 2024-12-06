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

## Yum install `ansible`, `ansible-core`, `python3-pip`, and `@Container Tools`:
```
sudo yum install -y ansible ansible-core python3-pip @Container tools
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

## Install `ansible-navigator` using python3-pip:
```
pip3 install ansible-navigator
```

## Pass the control node's public ssh key to the nodes:
```bash
cat << EOF > /home/rhel/ansible/playbooks/ssh_keys.yml
---
- name: Generate SSH key and distribute to nodes
  hosts: localhost
  tasks:
    - name: Check if SSH key pair exists
      stat:
        path: ~/.ssh/id_rsa
      register: ssh_key_stat

    - name: Generate SSH key pair
      command: ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -q -N ""
      when: not ssh_key_stat.stat.exists

- name: Distribute SSH public key to all nodes
  hosts: all
  gather_facts: no
  tasks:
    - name: Fetch SSH public key from control node
      delegate_to: localhost
      slurp:
        src: ~/.ssh/id_rsa.pub
      register: public_key_content

    - name: Authorize SSH key for nodes
      authorized_key:
        user: rhel  # Replace with appropriate username on target nodes
        key: "{{ public_key_content.content | b64decode }}"
EOF
```

## Use Ansible-Navigator to kick off the ssh_keys.yml file (IN YOUR ~/.BASHRC, nav="ansible-navigator run -m stdout"):
```bash
nav /home/rhel/ansible/playbooks/ssh_keys.yml
```
