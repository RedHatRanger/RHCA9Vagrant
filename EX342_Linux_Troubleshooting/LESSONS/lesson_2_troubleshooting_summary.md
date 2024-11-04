
# Lesson 2: Collecting Information to Support Troubleshooting

## Objectives
- Collect system information to support troubleshooting.

## Summary of Key Concepts

### 1. Locating Troubleshooting Information
- **Journals and Log Files:** Journals and log files contain messages from applications, services, and the kernel documenting events, errors, and changes.
  - **systemd-journald**: Stores binary-structured logs.
  - **rsyslog**: A central message hub for logging.
  - Some applications write private log files independently.

### 2. Viewing Journal Log Files
- Use the `journalctl` command to view log files.
- Filter logs for specific services, PIDs, or devices:
  ```bash
  journalctl /dev/vda
  journalctl -b _SYSTEMD_UNIT=httpd.service
  ```
- View logs from previous boot sessions:
  ```bash
  journalctl --list-boots
  journalctl --boot <boot_id> _SYSTEMD_UNIT=httpd.service
  ```

### 3. Configuring Journald for Persistent Storage
- Journald logs can be made persistent by creating `/var/log/journal` and updating `/etc/systemd/journald.conf`:
  ```bash
  mkdir /var/log/journal
  sed -i 's/#Storage=auto/Storage=persistent/' /etc/systemd/journald.conf
  systemctl restart systemd-journald.service
  ```

### 4. rsyslog Log Files
- **rsyslog**: Processes messages sent directly or via journald.
- Logs stored in `/var/log/` include:
  - **/var/log/messages**: General system messages.
  - **/var/log/secure**: Security-related logs.
  - **/var/log/maillog**: Mail server logs.
  - **/var/log/cron**: Cron jobs logs.
  - **/var/log/boot.log**: Boot log messages.

### 5. Private Log Files
- Some applications maintain private logs, e.g., Apache (`/var/log/httpd`) and Samba (`/var/log/samba`).

### 6. Enabling Verbose Information
- Increase logging verbosity using `-v`, `-vv`, `--debug`, etc., for commands or services.

### 7. Troubleshooting SELinux Events
- View SELinux audit logs: `/var/log/audit/audit.log`.
- Use `ausearch` to analyze SELinux logs for denials.
- Use `sealert` for guidance on resolving issues.
- Fix SELinux context with `semanage fcontext` and `restorecon` commands:
  ```bash
  semanage fcontext -a -t httpd_sys_content_t "/home/user/myweb"
  restorecon -R -v /home/user/myweb
  ```
- Enable SELinux Boolean:
  ```bash
  setsebool httpd_enable_homedirs on
  ```
- Allow services to use non-standard ports:
  ```bash
  semanage port -a -t http_port_t -p tcp 9876
  ```

## Sample Exercises

### 1. Exercise: View All System Logs
- Use the `journalctl` command to view all logs.
  ```bash
  journalctl
  ```
- **Question:** What type of events do you see? Identify one kernel event.

### 2. Exercise: Filter Journal Logs by Service
- View logs related to the `httpd` service:
  ```bash
  journalctl -b _SYSTEMD_UNIT=httpd.service
  ```
- **Question:** What is the status of the `httpd` service during the last boot?

### 3. Exercise: Configure Persistent Journald Logs
- Set up persistent storage for journal logs:
  1. Create `/var/log/journal`:
     ```bash
     mkdir /var/log/journal
     ```
  2. Edit `/etc/systemd/journald.conf` to enable persistent storage:
     ```bash
     sed -i 's/#Storage=auto/Storage=persistent/' /etc/systemd/journald.conf
     ```
  3. Restart `systemd-journald`:
     ```bash
     systemctl restart systemd-journald.service
     ```
- **Question:** How can you verify that journald logs are now stored persistently?

### 4. Exercise: Troubleshoot SELinux Denials
- Use `ausearch` to find SELinux denials.
  ```bash
  ausearch -m avc -ts recent
  ```
- **Question:** What SELinux denial did you observe? How would you fix it?

### 5. Exercise: Modify SELinux Context for Non-standard Directory
- Assign the correct context to a custom directory used by Apache:
  ```bash
  semanage fcontext -a -t httpd_sys_content_t "/home/user/myweb"
  restorecon -R -v /home/user/myweb
  ```
- **Question:** What command verifies that the new SELinux context has been applied correctly?
