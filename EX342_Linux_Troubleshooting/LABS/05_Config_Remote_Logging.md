
# Lab 6: Configuring Remote Logging to a Central Log Host

## Objective

- You should be able to centralize remote system logging to a central log host to efficiently manage logs, analyze events, and improve monitoring.

### Initial Setup

1. **Log in to node1 and Switch to the Root User**

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   ```

2. **Verify rsyslog is Running and Enabled at Boot**

   Confirm that the rsyslog service is active and set to start automatically:

   ```bash
   [root@node1 ~]# systemctl is-active rsyslog
   active
   [root@node1 ~]# systemctl is-enabled rsyslog
   enabled
   ```

3. **Configure node1 as the Central Log Host**

   - Edit `/etc/rsyslog.conf` to enable TCP reception and configure log separation by host and facility:

   ```bash
   [root@node1 ~]# vim /etc/rsyslog.conf
   
   # Enable TCP reception:
   module(load="imtcp")
   input(type="imtcp" port="514")
   
   # Add template to organize logs by host and facility:
   template(name="ExerciseTemplate" type="string"
            string="/var/log/loghost/%HOSTNAME%/%syslogfacility-text%.log")
   *.* action(type="omfile" DynaFile="ExerciseTemplate")
   ```

4. **Restart rsyslog to Apply Changes**

   ```bash
   [root@node1 ~]# systemctl restart rsyslog
   ```

5. **Configure Log Rotation**

   Add new log files to the log rotation schedule by editing `/etc/logrotate.d/syslog`:

   ```bash
   /var/log/loghost/*/*.log
   ```

6. **Configure Firewall on node1**

   Allow TCP traffic on port 514 to accept remote logs:

   ```bash
   [root@node1 ~]# firewall-cmd --add-port=514/tcp --permanent
   success
   [root@node1 ~]# firewall-cmd --reload
   ```

### Configuring Remote Logging from node2

7. **Log in to node2 and Switch to the Root User**

   ```bash
   [bob@workstation ~]$ ssh bob@node2
   [bob@node2 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node2 ~]#
   ```

8. **Configure rsyslog on node2 for Remote Logging**

   Add a rule to `/etc/rsyslog.conf` to forward syslog messages to node1 using TCP:

   ```bash
   [root@node2 ~]# vim /etc/rsyslog.conf
   
   # Send all messages to node1 using TCP:
   *.* action(type="omfwd" target="node1" port="514" protocol="tcp")
   ```

9. **Restart rsyslog on node2**

   ```bash
   [root@node2 ~]# systemctl restart rsyslog
   ```

### Testing Remote Logging

10. **Generate Test Log Messages from node2**

    Use the `logger` command to create test messages on node2:

    ```bash
    [root@node2 ~]# logger -p user.info "Test user.info message from node2"
    [root@node2 ~]# logger -p cron.crit "Test cron.crit message from node2"
    ```

11. **Verify Logs on node1**

    Log back into node1 and check that the messages from node2 were received and stored in the correct log files:

    ```bash
    [bob@workstation ~]$ ssh bob@node1
    [bob@node1 ~]$ sudo -i
    [root@node1 ~]# grep 'user\.info' /var/log/loghost/node2/user.log
    Dec 11 00:44:09 node2 root: Test user.info message from node2
    
    [root@node1 ~]# grep 'cron\.crit' /var/log/loghost/node2/cron.log
    Dec 11 00:44:40 node2 root: Test cron.crit message from node2
    ```

* Done!!
