# EXAM A: Test Your Knowledge Lab Exercise for Labs 1-7

## Objective
- Configure terminal session recording, central logging, and file integrity monitoring.
- Use both manual configurations and Ansible automation to configure these items.

### Lab Overview
In this lab exercise, you will use a combination of manual configuration via the web console and Ansible Playbooks to automate tasks across multiple servers.

- **node1** will be set up as a central log host.
- **node2** will be configured to send logs to node1.
- **workstation** will be used as the control node for executing Ansible Playbooks on the managed nodes (node1 and node2).
- Terminal session recording will be configured for a bob user on **node1**.
- **AIDE** will be used on **node2** for monitoring file integrity.

### Lab Exercise Questions

#### Instructions
Use the web console to configure terminal session recording for the bob user. Configure workstation as a control node, and verify communication with node1 and node2 as managed nodes. Use Ansible Playbooks to configure the managed nodes:

node1
- Configure terminal session recording for the bob user by using the web console.
- Configure the rsyslog service to serve as a central log host.
- Create a rule that writes the syslog messages that each host generates to separate files under the /var/log/loghost directory.
- Use the rsyslog.conf.j2 template file to configure syslog to create subdirectories that are based on the originating host name of each syslog message.
- The template file configures syslog to create subdirectories, and to maintain a separate log file for each syslog facility.
- Add the new log files to the log rotation schedule for size management.

node2
- Install AIDE to report file and directory changes.
- Configure node2 to send log messages to the central log host.
- Configure terminal session recording for the bob user.
- Configure workstation as a control node and node1 and node2 as managed nodes. Verify that the control node can reach both.
- Use an Ansible Playbook to configure node1 as a central log host. Use the provided configuration template.
- Use an Ansible Playbook to configure node2 as a remote log server, and configure AIDE to monitor file integrity. Use the provided configuration templates for each service.
- Run the playbook, and then view remote logging and file integrity on node2.

(scroll down for an answer)
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

### Lab Steps

#### Step 1: Configure Terminal Session Recording for the bob User

1. Log in to **node1** via the web console.
   - Open a web browser on **workstation** and navigate to `https://node1:9090`.
   - Use the username **bob** and password **redhat** to log in.

2. Configure terminal session recording for the **bob** user.
   - From the left menu, click Session Recording, and then click the gear icon.
   - Navigate to SSSD Config and select Scope > Some.
   - Then, define "bob" as Users and Groups to be monitored.
   - Click **Save** to apply the settings.

![image](https://github.com/user-attachments/assets/0560c0e2-c329-40af-91db-417aa267e09c)
("bob" is actually "bob" here.)

3. On workstation, open another terminal and log into node1 as the bob user.
```bash
[bob@workstation ~]$ ssh bob@node1
[bob@node1 ~]$
```
   - Run some commands as the bob user, such as ps auxf, top, and df -h.
```bash
[bob@node1 ~]$ ps auxf
...output omitted...
[bob@node1 ~]$ top
...output omitted...
[bob@node1 ~]$ df -h
...output omitted...
```

4. Return to workstation as bob.
```bash
[bob@node1 ~]$ exit
[bob@workstation ~]$
```

5. Confirm the terminal session recording. In the web console of node1, click Session Recording.
   - Refresh the page if necessary.
   - Click the bob user's recorded terminal session.

![image](https://github.com/user-attachments/assets/e707b651-1d1b-47bf-9475-f6e51a836008)
("bob" is actually "bob" here.)

6. Play back the recorded session by clicking Play.

![image](https://github.com/user-attachments/assets/caa699e4-ae8f-462c-b849-399143566d79)

#### Step 2: Configure Workstation as Control Node and Verify Managed Nodes

1. On **workstation**, configure it as an Ansible control node.
   - Install Ansible:
     ```bash
     sudo yum install ansible
     ```

2. Create an inventory file named `inventory` inside a directory named `exam_a` on **workstation**:
   ```ini
   [servers]
   central_loghost ansible_host=node1
   remote_loghost ansible_host=node2
   ```

3. Setup the `ansible.cfg` file:
   ```bash
   cd exam_a
   vim ansible.cfg
   ```

   ```ini
   [defaults]
   inventory = inventory
   remote_user = root
   host_key_checking = False
   deprecation_warnings = False
   ```
3. Verify communication between the control node and managed nodes by running a ping command:
   ```bash
   ansible -m ping all
   ...output omitted...
   ```
   
4. Use the provided `rsyslog.conf.j2` template file, which will configure syslog to create subdirectories based on the originating hostname of each log message.

#### rsyslog.conf.j2 Template

To create the `rsyslog.conf.j2` file, use the following command on the workstation:

```bash
cat << EOF > rsyslog.conf.j2
#### MODULES ####
module(load="imuxsock")  # provides support for local system logging (e.g. via logger command)
module(load="imklog")    # provides kernel logging support (previously done by rklogd)
module(load="immark")    # provides --MARK-- message capability
module(load="imtcp")
input(type="imtcp" port="514")

#### GLOBAL DIRECTIVES ####
# Use default timestamp format
global(exam_aectory="/var/spool/rsyslog")

#### RULES ####
# Log all kernel messages to the console.
# Logging much else clutters up the screen.
kern.* /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!.
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  /var/log/maillog

# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Create dynamic directories for remote hosts
$template HostTemplate,"/var/log/loghost/%HOSTNAME%/%syslogfacility-text%.log"
*.* ?HostTemplate
EOF
```

#### aide.conf.j2 Template

5. Create the `aide.conf.j2` file, use the following command and content:
```bash
cat << EOF > aide.conf.j2
@@define DBDIR /var/lib/aide
@@define LOGDIR /var/log/aide
@@define DBFILE "/var/lib/aide/aide.db.gz"
@@define REPORTFILE "/var/log/aide/aide.log"
@@define DATABASE_OUT "/var/lib/aide/aide.db.new.gz"

# Set the rules for which files/directories to monitor
/var/log p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512
/etc p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512
/bin p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512
/sbin p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512
/lib p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512
/lib64 p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha512

# Report all changes in the monitored directories
ALLXTRAHASHES = sha512
EOF
```

#### Step 3: Use an Ansible Playbook to configure `node1` as a central log host.


1. Inside the `exam_a` directory, create the `mybaseline.yaml` Playbook for the
rsyslog service configuration on `node1`:
   ```yaml
   # To execute: ansible-playbook mybaseline.yaml
   ---
   - name: Configure central loghost
     hosts: central_loghost
     gather_facts: False
     tasks:
       - name: Install rsyslog
         ansible.builtin.yum:
           name: rsyslog
           state: present
   
       - name: Ensure that rsyslog is active and enabled
         ansible.builtin.service:
           name: rsyslog
           state: started
           enabled: True
       
       - name: Copy rsyslog configuration template
         template:
           src: rsyslog.conf.j2
           dest: /etc/rsyslog.conf
           owner: root
           group: root
           mode: '0444'

       - name: Restart rsyslog
         service:
           name: rsyslog
           state: restarted

       - name: Open rsyslog firewalld port
         firewalld:
           port: 514/tcp
           immediate: yes
           permanent: yes
           state: enabled

       - name: Add entry for that the new logs have rotation
         lineinfile:
           path: /etc/logrotate.d/syslog
           line: /var/log/loghost/*/*.log

   - name: Configure remote logging
     hosts: remote_loghost
     gather_facts: False
     tasks:
       - name: Install rsyslog
         ansible.builtin.yum:
           name: rsyslog
           state: present
   
       - name: Ensure that rsyslog is active and enabled
         ansible.builtin.service:
           name: rsyslog
           state: started
           enabled: True
   
       - name: Add entry for redirecting logs
         lineinfile:
           path: /etc/rsyslog.conf
           line: '*.* action(type="omfwd" target="node1" port="514" protocol="tcp")'

       - name: Restart rsyslog service
         ansible.builtin.service:
           name: rsyslog
           state: restarted

   - name: Install AIDE
     hosts: remote_loghost
     gather_facts: False
     tasks:
       - name: Ensure that AIDE package is installed
         ansible.builtin.yum:
           name: aide
           state: present
   
       - name: Template out AIDE configuration file
         ansible.builtin.template:
           src: aide.conf.j2
           dest: /etc/aide.conf
           owner: root
           group: root
           mode: '0444'

       - name: Init AIDE database
         ansible.builtin.command: aide --init

       - name: Enable AIDE database
         ansible.builtin.command: mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
           tags: enable_aidedb
   ```

#### Step 5: Run the Playbooks and Verify Configurations

1. Run the playbooks from **workstation** to configure **node1** and **node2**:
   ```bash
   [bob@workstation exam_a]$ ansible-playbook mybaseline.yaml
   ```

2. On `workstation`, open another terminal and log into `node2` as the `bob` user:
[bob@workstation exam_a]$ ssh bob@node2
[bob@node2 ~]$

3. Generate some syslog messages with different log facilities and priorities. Then, return
to workstation as `bob`:
```bash
[bob@node2 ~]$ logger -p user.info "Test user.info message from node2"
[bob@node2 ~]$ logger -p cron.crit "Test cron.crit message from node2"
[bob@node2 ~]$ exit
[bob@workstation exam_a]$
```

4. Log into node1 and use sudo to verify the remote logging. Use bob as
password when prompted.
```bash
[bob@workstation exam_a]$ ssh bob@node1
[bob@node1 ~]$ sudo grep bob /var/log/loghost/node2/user.log
[sudo] password for bob: redhat
Oct 31 00:09:37 node2 bob[39113]: Test user.info message from node2

[bob@node1 ~]$ sudo grep bob /var/log/loghost/node2/cron.log
Sep 22 00:09:56 node2 bob[39202]: Test cron.crit message from node2
```

5. Return to `workstation` as `bob`:
```bash
[bob@node1 ~]$ exit
[bob@workstation exam_a]$
```

6. SSH to node2, using sudo to verify the integrity of the monitored files:
```bash
[bob@workstation exam_a]$ ssh bob@node2
[bob@node2 ~]$ sudo aide --check
...output omitted...
```

### Completion
You have successfully completed the lab exercise by configuring terminal session recording, setting up a central log server, and configuring a managed node to send log messages and monitor file integrity. Well done!
