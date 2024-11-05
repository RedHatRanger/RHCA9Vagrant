# EXAM A: Test Your Knowledge Lab Exercise

## Objective

- Configure terminal session recording, central logging, and file integrity monitoring.
- Use both manual configurations and Ansible automation to configure these items.

### Lab Overview

In this lab exercise, you will use a combination of manual configuration via the web console and Ansible Playbooks to automate tasks across multiple servers.

- node1 will be set up as a central log host.

- node2 will be configured to send logs to node1.

- workstation will be used as the Ansible control node for executing Playbooks on the managed nodes (node1 and node2).

- Terminal session recording will be configured for the bob user on node1.

- AIDE will be used on node2 for monitoring file integrity.

## Lab Exercise Questions

###Instructions

Use the web console to configure terminal session recording for the consultant user. Configure workstation as a control node, and verify communication with servera and serverb as managed nodes. Use Ansible Playbooks to configure the managed nodes:

servera

Configure terminal session recording for the consultant user by using the web console.

Configure the rsyslog service to serve as a central log host.

Create a rule that writes the syslog messages that each host generates to separate files under the /var/log/loghost directory.

Use the rsyslog.conf.j2 template file to configure syslog to create subdirectories that are based on the originating host name of each syslog message.

The template file configures syslog to create subdirectories, and to maintain a separate log file for each syslog facility.

Add the new log files to the log rotation schedule for size management.

serverb

Install AIDE to report file and directory changes.

Configure serverb to send log messages to the central log host.

Configure terminal session recording for the consultant user.

Configure workstation as a control node and servera and serverb as managed nodes. Verify that the control node can reach both.

Use an Ansible Playbook to configure servera as a central log host. Use the provided configuration template.

Use an Ansible Playbook to configure serverb as a remote log server, and configure AIDE to monitor file integrity. Use the provided configuration templates for each service.

Run the playbook, and then view remote logging and file integrity on serverb.

Lab Steps

Step 1: Configure Terminal Session Recording for the Consultant User

Log in to node1 via the web console.

Open a web browser on workstation and navigate to https://node1:9090.

Use the username bob and password redhat to log in.

Configure terminal session recording for the bob user.

Navigate to Session Recording in the web console.

Click the gear icon to modify the configuration.

Set Scope to "Some" and specify bob as the user to be monitored.

Click Save to apply the settings.

Step 2: Configure Workstation as Control Node and Verify Managed Nodes

On workstation, configure it as an Ansible control node.

Install Ansible:

sudo yum install ansible

Create an inventory file named inventory in a directory named lab_playbooks on workstation:

[webservers]
node1 ansible_host=node1

[logservers]
node2 ansible_host=node2

Verify communication between the control node and managed nodes by running a ping command:

ansible all -m ping -i lab_playbooks/inventory

Step 3: Configure node1 as a Central Log Host

Create an Ansible Playbook named configure_central_log.yaml in the lab_playbooks directory to configure node1:

- name: Configure node1 as central log host
  hosts: node1
  tasks:
    - name: Install rsyslog
      yum:
        name: rsyslog
        state: present
    
    - name: Enable TCP syslog reception
      lineinfile:
        path: /etc/rsyslog.conf
        line: 'module(load="imtcp")\ninput(type="imtcp" port="514")'
        create: yes

    - name: Copy rsyslog configuration template
      template:
        src: rsyslog.conf.j2
        dest: /etc/rsyslog.conf
        owner: root
        group: root
        mode: '0644'

    - name: Restart rsyslog
      service:
        name: rsyslog
        state: restarted

    - name: Add new log files to logrotate
      lineinfile:
        path: /etc/logrotate.d/syslog
        line: '/var/log/loghost/*/*.log'

Use the provided rsyslog.conf.j2 template file, which will configure syslog to create subdirectories based on the originating hostname of each log message.

Step 4: Configure node2 for Remote Logging and File Integrity Monitoring

Create an Ansible Playbook named configure_node2.yaml in the lab_playbooks directory to configure node2:

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

Step 5: Run the Playbooks and Verify Configurations

Run the playbooks from workstation to configure node1 and node2:

ansible-playbook -i lab_playbooks/inventory lab_playbooks/configure_central_log.yaml
ansible-playbook -i lab_playbooks/inventory lab_playbooks/configure_node2.yaml

Verify that node2 is sending logs to node1:

On node1, check the /var/log/loghost/node2 directory for incoming log messages:

ls /var/log/loghost/node2

Verify that AIDE is properly initialized and functioning on node2:

Run an AIDE check on node2:

aide --check

Check for any discrepancies between the file system and the AIDE database.

Completion

You have successfully completed the lab exercise by configuring terminal session recording, setting up a central log server, and configuring a managed node to send log messages and monitor file integrity. Well done!
