
# Lab 5: Configuring Remote Logging to a Central Log Host

## Objective

- You should be able to configure systems for remote logging to a central log host to centralize log management, extend log retention, and improve monitoring capabilities.

### Initial Setup

1. **Log in to node1 and switch to the root user**

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   ```

2. **Install and Configure the Central Log Host**

   On node1, you will configure it to accept remote logs:

   - Uncomment the following lines in `/etc/rsyslog.conf` to allow TCP or UDP reception of logs from remote systems.

   ```bash
   [root@node1 ~]# vim /etc/rsyslog.conf
   # For UDP reception:
   module(load="imudp")
   input(type="imudp" port="514")

   # For TCP reception:
   module(load="imtcp")
   input(type="imtcp" port="514")
   ```

3. **Configure Firewall Rules**

   Add the appropriate firewall rules to allow incoming log traffic on port 514:

   ```bash
   [root@node1 ~]# firewall-cmd --add-port=514/udp --permanent
   [root@node1 ~]# firewall-cmd --add-port=514/tcp --permanent
   [root@node1 ~]# firewall-cmd --reload
   ```

4. **Restart the rsyslog Service**

   Restart the rsyslog service to apply the changes:

   ```bash
   [root@node1 ~]# systemctl restart rsyslog
   ```

### Configuring Remote Clients to Forward Logs

5. **Log in to node2 and Switch to Root User**

   ```bash
   [bob@workstation ~]$ ssh bob@node2
   [bob@node2 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node2 ~]#
   ```

6. **Configure rsyslog on node2 to Send Logs to node1**

   Add the following entries to the `/etc/rsyslog.conf` file on node2 to forward logs to the central log host (node1):

   ```bash
   [root@node2 ~]# vim /etc/rsyslog.conf
   
   # Send all messages to the central log host using UDP:
   *.info action(type="omfwd" target="node1" port="514" protocol="udp")

   # Alternatively, use TCP for reliable delivery:
   *.* action(type="omfwd" target="node1" port="514" protocol="tcp")
   ```

7. **Restart the rsyslog Service on node2**

   Restart the rsyslog service on node2 to apply the changes:

   ```bash
   [root@node2 ~]# systemctl restart rsyslog
   ```

### Testing Remote Logging

8. **Send a Test Log Message from node2**

   Use the `logger` command to send a test message to node1 from node2:

   ```bash
   [root@node2 ~]# logger "Test log message from node2"
   ```

9. **Verify the Log Message on node1**

   Log back into node1 and verify that the message was received:

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$ sudo -i
   [root@node1 ~]# grep "Test log message from node2" /var/log/loghost/*
   ```

### Exporting Session Recordings to Central Log Host

10. **Install systemd-journal-remote on node1**

   To centralize session recordings, install the `systemd-journal-remote` package:

   ```bash
   [root@node1 ~]# yum install -y systemd-journal-remote
   ```

11. **Export Session Recordings**

   Create a temporary directory where the journal output with all its entities are exported into a file:

   ```bash
   [root@node1 ~]# mkdir /tmp/dir
   [root@node1 ~]# journalctl -o export | /usr/lib/systemd/systemd-journal-remote -o /tmp/dir/example.journal -
   ```

12. **Store Recordings in a Central Directory**

   On node1, create a directory to store session recording files and move the exported sessions to that directory:

   ```bash
   [root@node1 ~]# mkdir /var/log/journal/remote/
   [root@node1 ~]# mv /tmp/dir/example.journal /var/log/journal/remote/
   ```

13. **Playback the Recorded Sessions**

   Use the `tlog-play` command from the `tlog` package to playback the recorded sessions:

   ```bash
   [root@node1 ~]# tlog-play -r journal -M TLOG_REC=<your-unique-host-id>
   ```

### References

- [rsyslog.conf(5) man page](http://man7.org/linux/man-pages/man5/rsyslog.conf.5.html)
- [Recording Sessions Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/htmlsingle/recording_sessions/index)

