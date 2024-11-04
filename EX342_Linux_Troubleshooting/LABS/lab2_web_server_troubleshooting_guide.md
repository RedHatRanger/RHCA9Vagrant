
# Lab 2: Use Log Files to Troubleshoot a Web Server Issue

## Objective
- You should be able to use log files to troubleshoot a web server issue.

### INITIAL SETUP of ```node1```:
```bash
ssh bob@node1
sudo su
# Create the test file with incorrect permissions and SELinux context
mkdir -p /var/www/html
cat <<EOF > /var/www/html/test.html
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
</body></html>
EOF
chmod 600 /var/www/html/test.html
chcon -t samba_share_t /var/www/html/test.html
systemctl restart httpd
```

### Problem

"The `bob` user reported that the file `http://node1.example.com/test.html` is not accessible from a web browser. The testing manager did not provide further details."

### Step 1: Reproduce the Problem

1.1. As **bob** on **workstation**, attempt to access the test file:
```bash
[bob@workstation ~]$ curl http://node1.example.com/test.html
```
**Expected Output:**
```html
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
</body></html>
```

*"The message indicates that access is denied, which means a **403 Forbidden** error."*

### Step 2: Gather Information About the Target Server

2.1. Log in to **node1** and switch to the root user:
```bash
[bob@workstation ~]$ ssh bob@node1
[bob@node1 ~]$ sudo -i
[sudo] password for bob: redhat
[root@node1 ~]#
```

2.2. Check the `/var/log/httpd/access_log` for any message about this failure:
```bash
[root@node1 ~]# grep test.html /var/log/httpd/access_log
```
**Output Example:**
```
172.25.250.9 - - [02/Sep/2021:22:39:46 -0400] "GET /test.html HTTP/1.1" 403 199 "-" "curl/7.61.1"
```

*"The **403** in the output confirms that access is being denied."*

2.3. Check `/var/log/httpd/error_log` for more details about the failure:
```bash
[root@node1 ~]# tail /var/log/httpd/error_log
```
**Output Example:**
```
[Thu Sep 02 22:39:46.533187 2021] [core:error] [pid 6082:tid 140537780700928] (13)Permission denied: [client 172.25.250.9:51002] AH00035: access to /test.html denied (filesystem path '/var/www/html/test.html') because search permissions are missing on a component of the path
```

*"The error message indicates that file permissions are preventing access to **test.html**."*

### Step 3: Fix File Permissions

3.1. Inspect the file permissions on `/var/www/html/test.html`:
```bash
[root@node1 ~]# ls -l /var/www/html/test.html
```
**Output Example:**
```
-rw-------. 1 root root 8 Sep 2 22:39 /var/www/html/test.html
```

3.2. Correct the permissions to make the file world-readable:
```bash
[root@node1 ~]# chmod 644 /var/www/html/test.html
```

3.3. Confirm the permissions have been updated:
```bash
[root@node1 ~]# ls -l /var/www/html/test.html
```
**Output Example:**
```
-rw-r--r--. 1 root root 8 Sep 2 23:49 /var/www/html/test.html
```

### Step 4: Test Access Again

4.1. Return to the **workstation** as the **bob** user:
```bash
[root@node1 ~]# exit
[bob@node1 ~]$ exit
[bob@workstation ~]$
```

4.2. Test access to the file again:
```bash
[bob@workstation ~]$ curl http://node1.example.com/test.html
```
**Expected Output:**
```html
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
</body></html>
```

*"File permissions have been corrected, but the problem remains. The likely culprit is **SELinux**."*

### Step 5: Check SELinux Context

5.1. Log in to **node1** as **root**:
```bash
[bob@workstation ~]$ ssh bob@node1
[bob@node1 ~]$ sudo -i
[sudo] password for bob: redhat
[root@node1 ~]#
```

5.2. Check the SELinux log for denials:
```bash
[root@node1 ~]# ausearch -i -m avc -ts today
```
**Output Example:**
```
type=AVC msg=audit(09/02/2021 22:39:46.532:4380) : avc: denied { getattr } for pid=6082 comm=httpd path=/var/www/html/test.html dev="vda3" ino=1067530 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:samba_share_t:s0 tclass=file permissive=0
```

*"The **test.html** file has an SELinux type of **samba_share_t**, which **httpd** is not allowed to access."*

### Step 6: Fix SELinux Context

6.1. Run a recursive **restorecon** on `/var/www` to correct the SELinux context:
```bash
[root@node1 ~]# restorecon -Rv /var/www
```

6.2. Return to the **workstation** as the **bob** user:
```bash
[root@node1 ~]# exit
[bob@node1 ~]$ exit
[bob@workstation ~]$
```

### Step 7: Test Access Again

7.1. Test access to the file again using `curl`:
```bash
[bob@workstation ~]$ curl http://node1.example.com/test.html
```
**Expected Output:**
```
ServerA
```
*"The file is now accessible."*

### Step 8: Finish the Lab

- On the **workstation** machine, use the lab command to complete this exercise:
  ```bash
  [bob@workstation ~]$ lab finish strategy-collectinginfo
  ```
  This ensures that resources from previous exercises do not impact upcoming exercises.
