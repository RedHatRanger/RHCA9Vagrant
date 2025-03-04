# Single-Node Implementation of OpenShift on Proxmox:
[Tutorial Video](https://www.youtube.com/watch?v=leJa9HmvdI0&t=13s) by Ryan Nix

1) Go to: `https://console.redhat.com`
2) Click `OpenShift` on the left side panel.
3) Click `Clusters`>`Create a Cluster`>`Datacenter`>`Create Cluster`.
4) In the `Install OpenShift with the Assisted Installer`:
```
Cluster name: sno
Base Domain: example.com

# Check the box to install Single-Node OpenShift
# Leave the OpenShift Version at the highest (Default option)
```
5) Click `Next` to continue the setup.
6) Click `Generate Discovery ISO`>`Full image file: Provision with physical media`.
7) Switch to your local machine's terminal and run `cat ~/.ssh/id_rsa.pub | pbcopy` to grab your public SSH key.
8) Paste the public SSH key you copied in Step #7 to the onscreen text box.
9) Click `Generate Discovery ISO`.
10) At this point, you have 2 options:
```
# Option A: Download the Discovery ISO
# Option B: Copy the Discovery ISO URL (BEST)

# Then you will go into your Proxmox server and upload the ISO
```
11) Open `Proxmox` and click on `pve`>`local (pve)`>`ISO Images`>`Download from URL`.
12) Paste the `Discovery URL` you copied from Step #10, and give the image a name: `discovery.iso`.
13) Click `Download`.
14) Go into your router's settings and look for `LAN Setup`>`Advanced`>`Address Reservation`.
15) Fill in the `IP Address` and `MAC Address` of the `sno` OpenShift Cluster.
16) Switch back to `Proxmox`>`Create VM`.
17) In the `General` tab, type `SNO` in the `Name` box.
18) Click `Next`.
19) In the `OS` tab>`Use CD/DVD disc image file (iso):`>Click the `ISO Image` dropdown.
20) Choose the `discovery.iso`.
21) Click `Next`.
22) In the `Disks` tab>`Disk size (GiB):`, type `120`.
23) Click `Next`.
24) In the `CPU` tab:
```
Sockets: 2
Cores: 4
```
25) Click `Next`.
26) In the `Memory` tab, type `32768`.
27) Click `Next`.
28) In the `Network` tab, paste the `MAC Address` from Step #14 (Router's settings).
29) Click `Next`.
30) In the `Confirm` tab, check the `Start after finish` box.
31) Then click `Finish`.
32) Now go to your Proxmox console to see the VM build progress on the `sno` machine.
33) Once it's built, you can go to `https://console.redhat.com` to your `Clusters` to check if the `sno` instance is there.
```
# The instance showing up may take a few minutes.
```
34) In the same console, you'll need to change the hostname from `localhost` to `sno`.
35) Click `Save` and then `Next`.
```
# Note: you might see NTP Warning, but keep going
```
36) Click `Next`.
37) Choose your correct subnet in the `Available Subnets` dropdown.
38) Fix the NTP error by adding `time.apple.com` in the NTP server box.
39) Click `Add` then click `Next`.
40) Click `Install Cluster`.
```
# Note: While the Cluster installation is progressing, you will want to download the kubeconfig 
```
41) Click `Download kubeconfig` to download that to your local system.
42) Download the `OpenShift Client Software` from [here](https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/4.9.9/). *Version is 4.9.9 currently.
```
# Download both for your local system and the RHEL system we will use later.
```
43) Open up ports on your router `Port Forwarding`:
![image](https://github.com/user-attachments/assets/d6e4413b-d205-4595-b9c9-6952dcd53919)


# Post Install Operations
[Tutorial Video](https://www.youtube.com/watch?v=leJa9HmvdI0&t=514s) by Ryan Nix

1) On your DNS Server, be sure to add the Host A (IP-based) records and the CNAME (web address) records which would route different web addresses to your IP host.
2) Switch back to the terminal of your local host and run:
```
export KUBECONFIG=~/Desktop/SNO\ Deployment/kubeconfig

# Test that it is working with:
oc get nodes
```
3) Switch back to `https://console.redhat.com` and go to `Clusters`.
4) Locate your cluster you created and click to open the console of the cluster.
5) Type in the `Username` (kubeadmin) and `Password` that is provided for your cluster from the `console.redhat.com` site.
6) Switch over to your local machine's terminal and run:
```
ssh sadmin@rhel

oc login --token=sha256-<redacted> --server=https://api.sno.openshift.com:6443

oc patch configs.imageregistry.operator.openshift.io/cluster \
    --type merge \
    --patch '{"spec":{"managementState":"Managed"}}'
```
7) Switch back to the `OpenShift Console` (where you logged into it)>`Configuration`>`OAuth Details`.
8) Click to add one of the `Identity Providers` from the dropdown (In this example he uses HTPasswd).
9) Switch back to your terminal and still ssh'd in `sadmin@rhel` as root, run:
```
certbot -d '*.apps.sno.openshifthelp.com' --manual --preferred-challenges dns certonly
```
10) Enter the number `2` to replace the certificate, then follow the instructions to add the TXT DNS record.
11) Press `Enter` to continue.
12) You'll need to re-run this command:
```
certbot -d '*.apps.sno.openshifthelp.com' --manual --preferred-challenges dns certonly
```
13) Enter the number `2` to replace the certificate again.
14) Run the following commnads:
```
oc whoami

oc create configmap letsencrypt-ca-04092022 \
    --from-file=ca-bundle.crt=/etc/letsencrypt/live/apps.sno.openshifthelp.com/fullchain.pem \
    -n openshift-config

oc patch proxy/cluster \
    --type=merge \
    --patch='{"spec":{"trustedCA":{"name":"letsencrypt-ca-04092022"}}}'

oc create secret tls letsencrypt-ca-secret-04092022 \
    --cert=/etc/letsencrypt/live/apps.sno.openshifthelp.com/fullchain.pem \
    --key=/etc/letsencrypt/live/apps.sno.openshifthelp.com/privkey.pem \
    -n openshift-ingress
```
