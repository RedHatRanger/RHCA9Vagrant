### Complete Instructions to Install and Configure Red Hat IdM in a Proxmox VM

#### VM Setup on Proxmox:

- **Name:** `ipa.lab.example.com`
- **ISO:** Red Hat Enterprise Linux ISO
- **RAM:** Minimum **4 GB** (Recommended: **8 GB+**)
- **CPU:** Minimum **2 cores** (Recommended: **4 cores+**)
- **Disk:** At least **30 GB** (Recommended: **50 GB+**)
- **Network:** Bridged network

#### OS Installation:

- Complete installation using RHEL ISO
- Set `root password` and create a `rhel` user which will be the main administrator for this lab.

#### Give root privileges to the rhel user you created in the setup
```bash
echo "rhel        ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers.d/rhel
```

#### Configure Network and Hostname:

Set the static IP and hostname:
```bash
hostnamectl set-hostname ipa.lab.example.com
nmcli con mod ens18 ipv4.addresses 192.168.1.201/24 ipv4.gateway 192.168.1.254 ipv4.dns 8.8.8.8 ipv4.dns 8.8.4.4 ipv4.method manual
nmcli con up ens18

# OR
nmtui
```
![image](https://github.com/user-attachments/assets/c54c3d06-7561-483b-b708-bda5c4319d8e)

Update `/etc/hosts`:
```bash
echo "192.168.1.201 ipa.lab.example.com ipa" >> /etc/hosts
```

#### Enable Required Repositories:
```bash
yum update -y
subscription-manager attach --auto
subscription-manager repos --enable=rhel-9-for-x86_64-baseos-rpms
subscription-manager repos --enable=rhel-9-for-x86_64-appstream-rpms

# Verify:
subscription-manager repos --list-enabled
```
#### Install Necessary Packages:
```bash
yum -y install ipa-server ipa-server-dns vim bash-completion
```

#### Configure Firewall and SELinux:
```bash
firewall-cmd --add-service={dns,freeipa-4,freeipa-ldap,freeipa-ldaps,freeipa-replication,kerberos,kpasswd,ntp,http,https} --permanent
firewall-cmd --reload

# Verify:
firewall-cmd --list-all

# Configure SELinux:
setenforce 1
sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config
```

#### Configure IdM Server with DNS:
Run:
```bash
ipa-server-install
```

Provide the following when prompted:
- Do you want to configure integrated DNS (BIND)? [no]: yes
- **Hostname:** `ipa.lab.example.com`
- **Domain:** `lab.example.com`
- **Realm name**: `LAB.EXAMPLE.COM`
- **Directory Manager password:** `RedHatRanger201@@`
- **Directory Manager password:** `RedHatRanger201@@`
- Do you want to configure DNS forwarders? [yes]: `yes`
- Do you want to configure these servers as DNS forwarders? [yes]: `yes`
- Enter an IP address for a DNS forwarder, or press Enter to skip: `(press ENTER)`
- **IP Address:** `192.168.1.201`
- **Forwarder (DNS):** (Your network's DNS or external DNS, e.g., `8.8.8.8`)
- **Forwarding policy:** forward only
- **Reverse DNS:** Accept the default (recommended)

#### Verify Installation:
```bash
kinit admin
ipa status
```

Access Web UI:
```
https://ipa.lab.example.com
```

Log in with your IPA `admin` credentials.

#### Client Integration (optional):
On clients:
```bash
yum install -y ipa-client
ipa-client-install --domain=lab.example.com --realm=LAB.EXAMPLE.COM --server=ipa.lab.example.com --mkhomedir
```

#### Maintenance and Monitoring:
- Regular updates:
```bash
yum update -y
```

- Backup IdM:
```bash
ipa-backup
```

- Check logs and status:
```bash
journalctl -u ipa
journalctl -u named
```

Your Red Hat IdM server on Proxmox VM (`ipa.example.com`) is now ready and configured.


