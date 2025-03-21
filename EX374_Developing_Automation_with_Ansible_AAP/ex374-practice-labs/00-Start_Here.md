# How to Setup Your Ansible Automation Platform Home Lab
[Link to the AAP Documentation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.5)

<br>

## Part 1: Ansible Automation Platform (Control Node) Setup:

### 1. Register as a [Red Hat Developer](https://developers.redhat.com/register)

### 2. Sign in and download the [Ansible Automation Platform Setup Bundle](https://developers.redhat.com/content-gateway/file/ansible/Ansible_Automation_Platform_2.5/ansible-automation-platform-setup-bundle-2.5-1-x86_64.tar.gz)

### 3. Open PowerShell and run the SCP command:
```PowerShell
cd .\Downloads\
scp .\ansible-automation-platform-setup-bundle-2.5-1-x86_64.tar.gz rhel@172.28.128.100:/home/rhel

# or whatever the full bundle name is...as of this day it is 2.5
```
### 4. From the Windows Host Machine, ssh to the Ansible `control` node as the `rhel` user using `PowerShell`:
```PowerShell
ssh rhel@172.28.128.100
```

### 5. Register Your Ansible Control Node:
```bash
sudo subscription-manager register
# Enter your developer username and password
sudo subscription-manager auto-attach
```

### 6. Yum install `ansible`, `ansible-core`, `git`, `python3`, `python3-pip`, `container-tools`, and `rhel-system-roles`:
```
sudo yum install -y git python3 python3-pip container-tools rhel-system-roles
```

### 7. Yum update the system and reboot:
```
sudo yum -y update
sudo init 6
```

### 8. Generate the ~/.bashrc file for shortcut access to the nodes:
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

### 9. Configure the ~/.vimrc file:
```bash
cat << EOF > /home/rhel/.vimrc
autocmd FileType yaml setlocal ai ts=2 sw=2 et
EOF
```

### 10. Generate the ansible.cfg file
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

### 11. Setup the important Ansible directories:
```bash
mkdir -p /home/rhel/ansible/{roles,collections,playbooks,templates}
```

### Build the Inventory:
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

### 12. Setup the `host_vars`:
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

### 13. Create a "defaults.yml" file inside the "group_vars" folder:
```bash
cat << EOF > group_vars/defaults.yml
---
ansible_user: rhel
ansible_python_interpreter: /usr/bin/python3
EOF
```

### 14. Install the `ansible.posix` and `community.general` collections:
```bash
ansible-galaxy collection install https://galaxy.ansible.com/download/ansible-posix-2.0.0.tar.gz -p /home/rhel/ansible/collections/
ansible-galaxy collection install https://galaxy.ansible.com/download/community-general-10.1.0.tar.gz -p /home/rhel/ansible/collections/
```

### 15. Install `ansible-navigator` using python3-pip:
```
pip3 install ansible-navigator
```

### 16. Pass the control node's public ssh key to the nodes:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -q -N ""
for i in {node1,node2,node3,gitlab}; do ssh-copy-id -i /home/rhel/.ssh/id_rsa.pub $i; done
```

### 17. Unzip the Ansible Automation Platform's tar.gz file:
```bash
cd /home/rhel
tar -xvzf /home/rhel/ansible-automation-platform-setup-bundle-2.5-1-x86_64.tar.gz

# or whatever the full bundle name is...as of this day it is 2.5
```

### 18. Edit the Ansible Automation Platform `inventory` file:
```bash
cd ansible-automation-platform-setup-bundle-2.5-1-x86_64
cp inventory inventory.bak
cat << EOF > inventory
# AAP Enterprise Installer Inventory for a Simple Lab Setup
# This setup includes:
# - Ansible Automation Platform server
# - GitLab server for version control
# - 3 execution nodes for running playbooks

# This section is for your AAP Controller host(s)
# -----------------------------------------------------
[automationcontroller]
control.example.com ansible_connection=local ansible_host=172.28.128.100 ansible_user=rhel ansible_ssh_private_key_file=/home/rhel/.ssh/id_rsa

# Group definitions for easier targeting
[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o GlobalKnownHostsFile=/dev/null'
admin_password='redhat'
pg_host='control.example.com'
pg_port=5432
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

registry_url='registry.redhat.io'
registry_username='<registry username>'
registry_password='<registry password>'

# Automation Gateway configuration
automationgateway_pg_host='control.example.com'
automationgateway_pg_port=5432
automationgateway_pg_database='automationgateway'
automationgateway_pg_username='automationgateway'
automationgateway_pg_password=''
automationgateway_pg_sslmode='prefer'

# This section is for your Redis Nodes
# -----------------------------------------------------
# This section is for your Redis Nodes
# -----------------------------------------------------
[redis]
control.example.com
control.example.com
control.example.com
control.example.com
control.example.com
control.example.com
# -----------------------------------------------------
# Optional: Define relationships for AAP
# This section is for your AAP Execution Nodes
# -----------------------------------------------------
[execution_nodes]
node1.example.com
node2.example.com
node3.example.com

[automationcontroller:vars]
peers=execution_nodes
EOF
```

<br><br><br>
# Part 2: GitLab Server Setup:

### 1. On your `Windows Host Machine`, open `Notepad` as `Administrator`.
### 2. Click `Open` and browse to `C:\Windows\System32\drivers\etc\`, then click `all` to see all file types.
### 3. Double-click the `hosts` file to view its contents.
### 4. Add the following information at the bottom and click `Ctrl + s` to save:
```
172.28.128.100   control.example.com   control
172.28.128.101   node1.example.com     node1
172.28.128.102   node2.example.com     node2
172.28.128.103   node3.example.com     node3
172.28.128.104   gitlab.example.com    gitlab
```

### 5. Install `Visual Studio Code (VSCode)` on `Windows Host Machine`:
```
Go to: https://code.visualstudio.com/
Download and install the latest version.
```

### 6. Install `Git` on `Windows Host Machine`, and add `C:\Program Files\Git\bin` to the `Path` System Variables:
```
Go to: https://gitforwindows.org/ and click the blue download button.
Install the Git Progam using the defaults, by continuously clicking Next or Ok.
Press Win + R on your keyboard to open the Run dialog.
Type: SystemPropertiesAdvanced
Hit Enter to open the Advanced System Settings dialog directly.
Click on Environment Variables.
Under System variables, find and double-click Path, then click New.
Type the path to Git's bin in the empty bottom line (C:\Program Files\Git\bin).
```

### 7. Back on the PowerShell Window, SSH from the `control` node to the `gitlab` server and install `Git`:
```bash
ssh rhel@gitlab
sudo subscription-manager register
sudo subscription-manager auto-attach
sudo yum install -y git
```

### 8. Setup the local git repo and global configuration (Example Only):
```bash
git init
git config --global user.email 'RedHatRanger@example.com'
git config --global user.name 'Bob Jones'
git config --global http.sslVerify false
```

### 9. Optionally pull down a GitHub and transfer it to GitLab:
```
git clone https://github.com/username/repository-name.git
cd repository-name
git remote add gitlab https://gitlab.com/username/repository-name.git

# To change the https to http for certificate issues
git remote set-url origin http://site.git
```

### 10. Uncover the Initial Root Password for GitLab:
```bash
sudo grep -i password: /etc/gitlab/initial_root_password | cut -d ":" -f2

# Copy the output as you will need it on the next step to sign in as root
```

### 11. Go to: `https://gitlab.example.com` and sign in as `root` and the `password you copied in the previous step`.

### 12. Go to: `https://gitlab.example.com/admin/users/root/edit` and change the default `root` password to `RedHatRanger`.

### 13. Create another user with a secure password, promote the user to Administrator, then log off of `root` and back in with the new user.

### 14. Play around with adding Repositories (Projects).

### 15. Generate the https:// certificate (Copy and Paste this into a .sh file and chmod+x it to execute this):
```bash
#!/bin/bash

# Variables
ROOT_CA_NAME="MyRootCA"                     # Name of the Root CA
ROOT_CA_KEY="/etc/gitlab/ssl/rootCA.key"    # Root CA private key
ROOT_CA_CERT="/etc/gitlab/ssl/rootCA.crt"   # Root CA certificate
DOMAIN="gitlab.example.com"                 # GitLab domain
SSL_DIR="/etc/gitlab/ssl"                   # Directory for SSL files
DAYS_VALID=825                              # Validity for GitLab certificate
GITLAB_CONFIG="/etc/gitlab/gitlab.rb"       # GitLab configuration file

# Step 1: Create SSL Directory
echo "Creating SSL directory at $SSL_DIR..."
sudo mkdir -p "$SSL_DIR"
sudo chmod 700 "$SSL_DIR"

# Step 2: Generate Root Certificate Authority
echo "Generating Root Certificate Authority..."
sudo openssl genrsa -out "$ROOT_CA_KEY" 4096
sudo openssl req -x509 -new -nodes -key "$ROOT_CA_KEY" -sha256 -days 1825 \
  -out "$ROOT_CA_CERT" \
  -subj "/C=US/ST=State/L=City/O=MyOrganization/CN=$ROOT_CA_NAME"

echo "Root CA created at $ROOT_CA_CERT."

# Step 3: Generate Certificate Signing Request (CSR) for GitLab
echo "Generating a certificate signing request (CSR) for $DOMAIN..."
sudo openssl genrsa -out "$SSL_DIR/$DOMAIN.key" 2048
sudo openssl req -new -key "$SSL_DIR/$DOMAIN.key" \
  -out "$SSL_DIR/$DOMAIN.csr" \
  -subj "/C=US/ST=State/L=City/O=MyOrganization/CN=$DOMAIN"

# Step 4: Sign the CSR with the Root CA
echo "Signing the CSR with the Root CA..."
sudo openssl x509 -req -in "$SSL_DIR/$DOMAIN.csr" -CA "$ROOT_CA_CERT" -CAkey "$ROOT_CA_KEY" \
  -CAcreateserial -out "$SSL_DIR/$DOMAIN.crt" -days $DAYS_VALID -sha256 \
  -extfile <(printf "subjectAltName=DNS:$DOMAIN,DNS:www.$DOMAIN")

# Set permissions for SSL files
sudo chmod 600 "$SSL_DIR/$DOMAIN.key"
sudo chmod 644 "$SSL_DIR/$DOMAIN.crt"
sudo chmod 644 "$ROOT_CA_CERT"

echo "Certificate for $DOMAIN created and signed."

# Step 5: Backup Existing GitLab Configuration
echo "Backing up GitLab configuration..."
sudo cp "$GITLAB_CONFIG" "${GITLAB_CONFIG}.bak.$(date +%F_%T)"

# Step 6: Update GitLab Configuration
echo "Updating GitLab configuration to use the signed certificate..."
sudo tee "$GITLAB_CONFIG" > /dev/null <<EOF
external_url "https://$DOMAIN"

# Configure NGINX with the signed certificate
nginx['ssl_certificate'] = "$SSL_DIR/$DOMAIN.crt"
nginx['ssl_certificate_key'] = "$SSL_DIR/$DOMAIN.key"
EOF

# Step 7: Reconfigure GitLab
echo "Reconfiguring GitLab to apply the new settings..."
sudo gitlab-ctl reconfigure
if [ $? -eq 0 ]; then
    echo "GitLab reconfiguration completed successfully."
else
    echo "Error: GitLab reconfiguration failed. Please check the logs."
    exit 1
fi

# Final Instructions
echo "Self-signed Root CA and GitLab SSL setup completed successfully!"
echo "Distribute the Root CA certificate ($ROOT_CA_CERT) to clients to trust the GitLab server."
echo "You can access your GitLab instance at: https://$DOMAIN"
```





