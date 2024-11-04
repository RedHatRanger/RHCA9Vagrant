# Lab 7: Guided Exercise - Configuring Change Tracking

## Objective

- Configure and perform system auditing with the Linux audit system using Advanced Intrusion Detection Environment (AIDE).

## Outcomes

- Detect changes to the content and attributes of files and directories on a file system.

## Instructions

### Step 1: Log in to Server and Switch to Root User

1. On the workstation as the student user, log in to `servera` and switch to the root user.

   ```bash
   [student@workstation ~]$ ssh student@servera
   ...output omitted...
   [student@servera ~]$ sudo -i
   [sudo] password for student: student
   [root@servera ~]#
   ```

### Step 2: Configure AIDE to Monitor File Integrity

1. Initialize the AIDE database.

   ```bash
   [root@servera ~]# aide -i
   ```

   **Output:**
   
   ```
   Start timestamp: 2021-09-14 22:00:07 -0400 (AIDE 0.16)
   AIDE initialized database at /var/lib/aide/aide.db.new.gz
   ...
   End timestamp: 2021-09-14 22:00:07 -0400 (run time: 0m 0s)
   ```

2. Rename the newly created AIDE database to remove the "new" substring.

   ```bash
   [root@servera ~]# mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
   ```

### Step 3: View Default Monitored Files and Rules

1. View the `/etc/aide.conf` configuration file to learn the default monitored files and rules.

   ```bash
   [root@servera ~]# cat /etc/aide.conf
   ```

   **Output (Partial):**
   
   ```
   # Custom Rules
   # Extended content + file type + access.
   CONTENT_EX = sha512+ftype+p+u+g+n+acl+selinux+xattrs
   /etc/hosts$ CONTENT_EX
   /etc/issue$ CONTENT_EX
   /etc/passwd$ CONTENT_EX
   /etc/group$ CONTENT_EX
   /etc/shadow$ CONTENT_EX
   ```

### Step 4: Generate File Integrity Report

1. Generate the AIDE file integrity report.

   ```bash
   [root@servera ~]# aide --check
   ```

   **Output:**
   
   ```
   Start timestamp: 2021-09-14 22:10:19 -0400 (AIDE 0.16)
   AIDE found NO differences between database and filesystem. Looks okay!!
   ...
   End timestamp: 2021-09-14 22:10:19 -0400 (run time: 0m 0s)
   ```

2. Verify the report is recorded in the log file.

   ```bash
   [root@servera ~]# cat /var/log/aide/aide.log
   ```

### Step 5: Create Alert Script

1. Create a shell script to send an email alert when changes are found in monitored files.

   ```bash
   [root@servera ~]# vim /root/aide_mon.sh
   ```

   **Content:**

   ```bash
   if ! grep "Looks okay" /var/log/aide/aide.log &>/dev/null
   then
     cat /var/log/aide/aide.log | /usr/bin/mail -s "AIDE Alert" student@servera.lab.example.com
   fi
   ```

2. Make the script executable.

   ```bash
   [root@servera ~]# chmod +x /root/aide_mon.sh
   ```

### Step 6: Schedule AIDE Execution with cron

1. Create the `/etc/cron.d/aide` file and add a cron job to run AIDE every two minutes.

   ```bash
   [root@servera ~]# vim /etc/cron.d/aide
   ```

   **Content:**

   ```cron
   # AIDE Checks and alert
   */2 * * * * root /sbin/aide --check && /root/aide_mon.sh
   ```

### Step 7: Verify Integrity Report

1. Verify that the integrity report is recorded in the log file, confirming no differences between the database and the file system.

   ```bash
   [root@servera ~]# cat /var/log/aide/aide.log
   ```

### Step 8: Trigger an Inconsistency by Adding a User

1. Add a new user to trigger changes in the monitored account database.

   ```bash
   [root@servera ~]# useradd user01
   ```

### Step 9: Check for AIDE Alert Email

1. Switch to the `student` user on `servera` and check for AIDE alert emails.

   ```bash
   [root@servera ~]# exit
   [student@servera ~]$ mail
   ```

   **Output:**
   
   ```
   Heirloom Mail version 12.5 7/5/10. Type ? for help.
   "/var/spool/mail/student": 1 message 1 new
   >N 1 root Tue Sep 14 22:54 62/2250 "AIDE Alert"
   ```

### Step 10: Update AIDE Database

1. Switch back to the root user.

   ```bash
   [student@servera ~]$ sudo -i
   ```

2. Update the AIDE database.

   ```bash
   [root@servera ~]# aide --update
   ```

3. Back up the current AIDE database.

   ```bash
   [root@servera ~]# mv /var/lib/aide/aide.db.gz /var/lib/aide/aide.db.old.gz
   ```

4. Rename the new database.

   ```bash
   [root@servera ~]# mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
   ```

### Step 11: Verify No New Alerts

1. Switch to the student user and confirm no new alerts are present.

   ```bash
   [root@servera ~]# exit
   [student@servera ~]$ mail
   ```

2. Verify that the integrity report shows no differences.

   ```bash
   [student@servera ~]$ sudo cat /var/log/aide/aide.log
   ```