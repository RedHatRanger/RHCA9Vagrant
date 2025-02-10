# Usage Steps

1. **Install VirtualBox and Vagrant** (if you haven’t already).
2. **Auto-register with Red Hat**  
   If you want the VMs to automatically register, set the following environment variables **before** running `vagrant up`:

- Linux:
```bash
   export RHSM_USER="<rhn_username>"
   export RHSM_PASS="<rhn_password>"
```

- Windows:
```
   $env:RHSM_USER = "<rhn_username>"
   $env:RHSM_PASS = "<rhn_password>"
```

Result
4 RHEL 9 VMs spin up:

controller (2 vCPUs, 4 GB RAM)
node1 (1 vCPU, 2 GB RAM, plus Podman container on port 8080)
db (1 vCPU, 2 GB RAM)
hub (1 vCPU, 2 GB RAM)
Each VM:

Creates a rhel user with password redhat.
Updates SSH config to allow password + public key authentication.
(Optionally) registers with subscription-manager.
Adds rhel to wheel with passwordless sudo.
On node1, runs a quick httpd container as a stand-in for a GitHub or any other container.
