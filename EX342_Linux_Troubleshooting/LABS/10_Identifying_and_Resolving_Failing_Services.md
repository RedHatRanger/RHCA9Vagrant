# Lab 10: Identifying and Resolving Failing Services

## Objective

- Resolve an issue where two services fail to start at boot.

## Outcomes

- You should be able to resolve an issue where two services fail to start at boot.

## Initial Setup

To prepare your system for this exercise, create the necessary drop-in configuration files for `httpd` and `vsftpd` services using the following commands:

```bash
[bob@workstation ~]$ ssh bob@node1
...output omitted...
[bob@node1 ~]$ sudo -i
[sudo] password for bob: redhat
[root@node1 ~]# yum install httpd vsftpd -y
[root@node1 ~]# mkdir -p /etc/systemd/system/httpd.service.d
[root@node1 ~]# mkdir -p /etc/systemd/system/vsftpd.service.d

cat << EOF > /etc/systemd/system/httpd.service.d/10-dependencies.conf
[Unit]
After=vsftpd.service
Requires=vsftpd.service
EOF

cat << EOF > /etc/systemd/system/vsftpd.service.d/10-dependencies.conf
[Unit]
After=httpd.service
Requires=httpd.service
EOF

[root@node1 ~]# systemctl daemon-reload
```

## Instructions

Your `node1` machine is set up to serve both web and FTP content with the `httpd` and `vsftpd` services. The specification states that both daemons should operate simultaneously on the same system. Your colleague attempted to meet this requirement, but now both services fail to start at boot time. You are asked to resolve this issue. You are only required to ensure that both services start. You do not need to verify or configure that both services always operate simultaneously.

### Step 1: Check the Status of Services

1. Log in to the `node1` system and switch to the root user:

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   ...output omitted...
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   ```

2. Check the status of the `httpd` service. Use the `-l` option to display full status lines:

   ```bash
   [root@node1 ~]# systemctl status -l httpd
   ● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Drop-In: /etc/systemd/system/httpd.service.d
            └─10-dependencies.conf
   Active: inactive (dead)
   Docs: man:httpd.service(8)
   Sep 24 21:50:42 node1.example.com systemd[1]: httpd.service: Found ordering cycle on vsftpd.service/start
   Sep 24 21:50:42 node1.example.com systemd[1]: httpd.service: Found dependency on httpd.service/start
   Sep 24 21:50:42 node1.example.com systemd[1]: httpd.service: Unable to break cycle starting with httpd.service/start
   Sep 24 21:50:42 node1.example.com systemd[1]: vsftpd.service: Found ordering cycle on httpd.service/start
   Sep 24 21:50:42 node1.example.com systemd[1]: vsftpd.service: Found dependency on vsftpd.service/start
   Sep 24 21:50:42 node1.example.com systemd[1]: vsftpd.service: Unable to break cycle starting with vsftpd.service/start
   ...output omitted...
   ```

3. Check the status of the `vsftpd` service:

   ```bash
   [root@node1 ~]# systemctl status -l vsftpd
   ● vsftpd.service - Vsftpd ftp daemon
   Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; enabled; vendor preset: disabled)
   Drop-In: /etc/systemd/system/vsftpd.service.d
            └─10-dependencies.conf
   Active: inactive (dead)
   ...output omitted...
   Sep 24 21:50:42 node1.example.com systemd[1]: httpd.service: Unable to break cycle starting with httpd.service/start
   ...output omitted...
   ```

### Step 2: Analyze Drop-In Configuration Files

Both services have start dependencies on each other. The `systemctl status` command output includes the files responsible for the dependencies.

1. Observe that two nonstandard drop-in files were added to the services:

   - `/etc/systemd/system/httpd.service.d/10-dependencies.conf`
   - `/etc/systemd/system/vsftpd.service.d/10-dependencies.conf`

2. View the contents of these two files:

   ```bash
   [root@node1 ~]# cat /etc/systemd/system/httpd.service.d/10-dependencies.conf
   [Unit]
   After=vsftpd.service
   Requires=vsftpd.service
   
   [root@node1 ~]# cat /etc/systemd/system/vsftpd.service.d/10-dependencies.conf
   [Unit]
   After=httpd.service
   Requires=httpd.service
   ```

### Step 3: Form a Hypothesis

Both services have a `Requires` directive for the other service, but this directive ensures only that if one of them is started, the other is started as well. However, both services also have an `After` requirement on the other service. This causes a cyclic dependency, which `systemd` resolves by not starting either service.

### Step 4: Possible Solutions

1. **Remove the drop-in directories and files entirely**, then reload `systemd` and start the services. This approach removes all dependencies.

2. **Change one of the `After` lines to `Before`** to ensure that the services are always started together, without causing a cyclic dependency.

### Step 5: Fix the Issue

Change the `After` line for the `httpd` service to `Before`. Then reload `systemd` and start both services to verify.

1. Edit `/etc/systemd/system/httpd.service.d/10-dependencies.conf` to match this text:

   ```ini
   [Unit]
   Before=vsftpd.service
   Requires=vsftpd.service
   ```

2. Reload the active `systemd` configuration:

   ```bash
   [root@node1 ~]# systemctl daemon-reload
   ```

3. Start the `httpd.service` and `vsftpd.service` services:

   ```bash
   [root@node1 ~]# systemctl start httpd.service vsftpd.service
   [root@node1 ~]#
   ```

### Step 6: Verify Services at Boot

1. Reboot `node1`:

   ```bash
   [root@node1 ~]# reboot
   Connection to node1 closed by remote host.
   Connection to node1 closed.
   [bob@workstation ~]$
   ```

2. Log in to `node1` and switch to the root user:

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   ...output omitted...
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: bob
   [root@node1 ~]#
   ```

3. Check that the `httpd` and `vsftpd` services are active:

   ```bash
   [root@node1 ~]# systemctl status httpd.service vsftpd.service
   ● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Drop-In: /etc/systemd/system/httpd.service.d
            └─10-dependencies.conf
   Active: active (running) since Fri 2021-09-24 23:22:35 EDT; 2min 23s ago
   ...output omitted...
   
   ● vsftpd.service - Vsftpd ftp daemon
   Loaded: loaded (/usr/lib/systemd/system/vsftpd.service; enabled; vendor preset: disabled)
   Drop-In: /etc/systemd/system/vsftpd.service.d
            └─10-dependencies.conf
   Active: active (running) since Fri 2021-09-24 23:22:35 EDT; 2min 23s ago
   ...output omitted...
   ```

