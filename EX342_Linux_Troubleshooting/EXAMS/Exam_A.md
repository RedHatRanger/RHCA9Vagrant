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
("consultant" is actually "bob" here.)

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

4. Return to workstation as student.
```bash
[bob@node1 ~]$ exit
[student@workstation ~]$
```

5. Confirm the terminal session recording. In the web console of node1, click Session Recording.
   - Refresh the page if necessary.
   - Click the bob user's recorded terminal session.

![image](https://github.com/user-attachments/assets/e707b651-1d1b-47bf-9475-f6e51a836008)
("consultant" is actually "bob" here.)

6. Play back the recorded session by clicking Play.

![image](https://github.com/user-attachments/assets/caa699e4-ae8f-462c-b849-399143566d79)

#### Step 2: Configure Workstation as Control Node and Verify Managed Nodes

1. On **workstation**, configure it as an Ansible control node.
   - Install Ansible:
     ```bash
     sudo yum install ansible
     ```

2. Create an inventory file named `inventory` in a directory named `exam_a` on **workstation**:
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

3. Use the provided `rsyslog.conf.j2` template file, which will configure syslog to create subdirectories based on the originating hostname of each log message.

#### rsyslog.conf.j2 Template

To create the `rsyslog.conf.j2` file, use the following command on the workstation:

```bash
cat << EOF > exam_a/rsyslog.conf.j2
# rsyslog.conf.j2 - rsyslog configuration template

module(load="imuxsock")    # provides support for local system logging
module(load="imklog")      # kernel logging support
module(load="imtcp")       # TCP listener for remote logs

input(type="imtcp" port="514")

$template RemoteLogs, "/var/log/loghost/%HOSTNAME%/%syslogfacility-text%.log"

*.* ?RemoteLogs

# Include default rules
include(file="/etc/rsyslog.d/*.conf")
EOF
```

#### Step 4: Configure node2 for Remote Logging and File Integrity Monitoring

1. Create an Ansible Playbook named `configure_node2.yaml` in the `exam_a` directory to configure **node2**:
   ```yaml
   - name: Configure node2 for remote logging and AIDE
     hosts: node2
     tasks:
       - name: Install AIDE
         yum:
           name: aide
           state: present

       - name: Configure node2 to send logs to node1
         lineinfile:
           path: /etc/rsyslog.conf
           line: '*.* action(type="omfwd" target="node1" port="514" protocol="tcp")'
           create: yes

       - name: Restart rsyslog
         service:
           name: rsyslog
           state: restarted

       - name: Initialize AIDE database
         command: aide --init

       - name: Rename AIDE database
         command: mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
   ```

#### Step 5: Run the Playbooks and Verify Configurations

1. Run the playbooks from **workstation** to configure **node1** and **node2**:
   ```bash
   ansible-playbook -i exam_a/inventory exam_a/configure_central_log.yaml
   ansible-playbook -i exam_a/inventory exam_a/configure_node2.yaml
   ```

2. Verify that **node2** is sending logs to **node1**:
   - On **node1**, check the `/var/log/loghost/node2` directory for incoming log messages:
     ```bash
     ls /var/log/loghost/node2
     ```

3. Verify that AIDE is properly initialized and functioning on **node2**:
   - Run an AIDE check on **node2**:
     ```bash
     aide --check
     ```
   - Check for any discrepancies between the file system and the AIDE database.

### Completion
You have successfully completed the lab exercise by configuring terminal session recording, setting up a central log server, and configuring a managed node to send log messages and monitor file integrity. Well done!
