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

![{DF1EF811-EC23-4CF9-8A93-F5FD9350DB29}](https://github.com/user-attachments/assets/816f2752-32ae-4f8d-8b14-f339352c60ab)



### Session Recording in the Web Console

7. **Configure the terminal session recording of the bob user**

   7.1. Click **Session Recording** from the main menu, and then click the gear icon. Navigate to **SSSD Config** and select **Scope > Some**, and then define `bob` as **Users and Groups**.

![{41FC2331-257A-4612-98BF-50E84F1A6424}](https://github.com/user-attachments/assets/03cf8db2-fa3f-4259-af52-052c87e48ad9)
* Change "consultant" to "bob" on both the users and the groups boxes

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

![{2840C697-554A-47C1-9E50-88FC30C98177}](https://github.com/user-attachments/assets/00d18f10-0b21-4704-8576-d4d0895fe4d9)


   9.3. Play back the recorded session. Click **Play**.

* Done!!
