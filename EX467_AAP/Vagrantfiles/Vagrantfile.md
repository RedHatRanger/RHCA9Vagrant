## Vagrant Setup for Lab Environment
Use the following `Vagrantfile` to provision your Controller and two node VMs in VirtualBox. This defines:
- **controller**: all-in-one AAP Controller, Postgres DB, and Private Hub on a single node
- **node1**, **node2**: managed hosts

```ruby
# Vagrantfile for a minimal, lab/testing setup of AAP 2.4 (offline bundle)
#
# Hostnames and IP addresses (with /24 CIDR):
# - controller.lab.example.com → 192.168.1.201 (Controller + DB + Hub)
# - node1.lab.example.com      → 192.168.1.211 (Managed Host)
# - node2.lab.example.com      → 192.168.1.212 (Managed Host)
#
# Box: "generic/rhel9" (widely used base RHEL 9 box from Vagrant Cloud).
#
# The password "redhat" is used for 'rhel' and 'root' for exam/lab practice.
# In production, use more secure credentials.
#
# On Linux/macOS, set:
#   export RHSM_USER="<rhn_username>"
#   export RHSM_PASS="<rhn_password>"
# On Windows (PowerShell):
#   $env:RHSM_USER = "<rhn_username>"
#   $env:RHSM_PASS = "<rhn_password>"
# Then run `vagrant up`. If not set, subscription registration is skipped.

Vagrant.configure("2") do |config|
  config.vm.box = "generic/rhel9"

  if ENV['RHSM_USER'].nil? || ENV['RHSM_PASS'].nil?
    puts "WARNING: RHSM_USER / RHSM_PASS not set. Subscription registration will be skipped."
  end

  # Common shell snippets
  subscription_provision = <<-SHELL
    if [ -z "$RHSM_USER" ] || [ -z "$RHSM_PASS" ]; then
      echo "Skipping subscription-manager..."
      exit 0
    fi
    sudo subscription-manager register --username "$RHSM_USER" --password "$RHSM_PASS" --auto-attach
  SHELL

  create_rhel_user = <<-SHELL
    if ! id rhel &>/dev/null; then
      sudo useradd -m -s /bin/bash rhel
    fi
  SHELL

  common_provision = <<-SHELL
    echo 'rhel:redhat' | sudo chpasswd
    echo 'root:redhat' | sudo chpasswd
    cat <<HOSTS | sudo tee -a /etc/hosts
192.168.1.201 controller.lab.example.com controller
192.168.1.211 node1.lab.example.com node1
192.168.1.212 node2.lab.example.com node2
HOSTS
  SHELL

  wheel_sudo = <<-SHELL
    sudo usermod -aG wheel rhel
    echo '%wheel ALL=(ALL) NOPASSWD: ALL' | sudo tee /etc/sudoers.d/wheel-nopasswd
    sudo chmod 440 /etc/sudoers.d/wheel-nopasswd
  SHELL

  # Controller node
  config.vm.define "controller" do |c|
    c.vm.hostname = "controller.lab.example.com"
    c.vm.network "private_network", ip: "192.168.1.201"
    c.vm.provider "virtualbox" do |vb|
      vb.memory = 8192
      vb.cpus   = 2
    end
    c.vm.provision "shell", inline: create_rhel_user
    c.vm.provision "shell", inline: common_provision
    c.vm.provision "shell", inline: subscription_provision, env: {
      "RHSM_USER" => ENV['RHSM_USER'],
      "RHSM_PASS" => ENV['RHSM_PASS']
    }
    c.vm.provision "shell", inline: wheel_sudo
    c.vm.provision "shell", inline: <<-SHELL
      sudo yum install -y ansible python3-pip container-tools
      sudo -u rhel python3 -m pip install --user ansible-navigator
    SHELL
  end

  # Managed node1
  config.vm.define "node1" do |n1|
    n1.vm.hostname = "node1.lab.example.com"
    n1.vm.network "private_network", ip: "192.168.1.211"
    n1.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus   = 1
    end
    n1.vm.provision "shell", inline: create_rhel_user
    n1.vm.provision "shell", inline: common_provision
    n1.vm.provision "shell", inline: subscription_provision, env: {
      "RHSM_USER" => ENV['RHSM_USER'],
      "RHSM_PASS" => ENV['RHSM_PASS']
    }
    n1.vm.provision "shell", inline: wheel_sudo
  end

  # Managed node2
  config.vm.define "node2" do |n2|
    n2.vm.hostname = "node2.lab.example.com"
    n2.vm.network "private_network", ip: "192.168.1.212"
    n2.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus   = 1
    end
    n2.vm.provision "shell", inline: create_rhel_user
    n2.vm.provision "shell", inline: common_provision
    n2.vm.provision "shell", inline: subscription_provision, env: {
      "RHSM_USER" => ENV['RHSM_USER'],
      "RHSM_PASS" => ENV['RHSM_PASS']
    }
    n2.vm.provision "shell`, inline: wheel_sudo
  end
end
```
