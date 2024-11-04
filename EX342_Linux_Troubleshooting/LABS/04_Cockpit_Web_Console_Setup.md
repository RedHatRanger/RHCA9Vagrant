# Lab 4: Installing and Configuring the Web Console on node1

## Objective

- You should be able to install and configure the web console software on node1, gather performance statistics, and use session recording to audit a user's actions.

### Initial Setup

1. **Log in to node1 and switch to the root user**

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   ```

2. **Install the web console software packages on node1**

   Install the following packages to extend the capabilities of the web console:

   ```bash
   [root@node1 ~]# yum install cockpit-composer cockpit-machines cockpit-pcp cockpit-podman cockpit-session-recording tlog
   ```

3. **Restart the pmlogger service to configure persistent logging of performance metrics**

   ```bash
   [root@node1 ~]# systemctl restart pmlogger
   ```

4. **Start and enable the web console**

   ```bash
   [root@node1 ~]# systemctl enable --now cockpit.socket
   ```

5. **Log out from the root user and from node1**

   ```bash
   [root@node1 ~]# exit
   [bob@node1 ~]$ logout
   Connection to node1 closed.
   [bob@workstation ~]$
   ```

### Inspecting System Performance in the Web Console

6. **Inspect the system performance status of node1 on the web console**

   6.1. On workstation, open a web browser and navigate to `https://node1:9090`. Accept the certificate warning. Log in with username `bob` and password `bob`.

   6.2. Review the alerts that are reported in the overview.

   6.3. Inspect the historical performance of the system. Click the **View details and history** link in the Usage box.

![{93954FC6-9EAA-4273-9363-FF2F4F502574}](https://github.com/user-attachments/assets/2d130e6d-4cd9-42b1-ab91-8176df5d14cd)


### Session Recording in the Web Console

7. **Configure the terminal session recording of the bob user**

   7.1. Click **Session Recording** from the main menu, and then click the gear icon. Navigate to **SSSD Config** and select **Scope > Some**, and then define `bob` as **Users and Groups**.

   7.2. Click **Save** and click **Session Recording** at the top of the page.

### Bob User Activity

8. **Log in as the bob user and perform some tasks on the node1 host to be monitored**

   8.1. Open a terminal and log in to node1 as the bob user:

   ```bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$
   ```

   8.2. Run various commands as the bob user:

   ```bash
   [bob@node1 ~]$ ps auxf
   [bob@node1 ~]$ top
   [bob@node1 ~]$ df -h
   ```

   8.3. Return to workstation as bob:

   ```bash
   [bob@node1 ~]$ exit
   [bob@workstation ~]$
   ```

### Reviewing Recorded Sessions

9. **Review the tasks that the bob user performed in the web console**

   9.1. In the web console of node1, click **Session Recording**.

   9.2. Click the recorded terminal session of the bob user.

   9.3. Play back the recorded session. Click **Play**.

### References

- [Managing Systems Using the RHEL 8 Web Console Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/htmlsingle/managing_systems_using_the_rhel_8_web_console/index)
- [Recording Sessions Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/htmlsingle/recording_sessions/index)
- [Cockpit Project](http://www.cockpit-project.org)
