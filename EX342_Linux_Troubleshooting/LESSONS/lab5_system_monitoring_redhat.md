
# LESSON 4: Monitoring Systems with Red Hat Tools

## Objective

- You should be able to monitor systems using the Red Hat web console and gather system information.

### Overview

The Red Hat Enterprise Linux 9 web console, built with Cockpit technology, provides an easy-to-use, web-based interface to manage and monitor Linux hosts. It allows administrators to perform system monitoring without requiring extensive command-line tool knowledge. By default, many RHEL 9 deployments include the web console, but it can also be installed manually if needed.

### Initial Setup

1. **Install the web console (Cockpit)**

   - If the Cockpit package (web console) is not installed by default, install it on your system:

   ```bash
   [root@host ~]# yum install cockpit
   ```

2. **Enable and Start the Cockpit Service**

   - Use the `systemctl` command to enable and start the `cockpit.socket` service, allowing access to the web console:

   ```bash
   [root@host ~]# systemctl enable --now cockpit.socket
   ```

3. **Access the Web Console**

   - Open a web browser and navigate to `https://<your-server-ip>:9090`.
   - Log in with a system username and password.
   - If logging in with a user account that has `sudo` privileges, select **Reuse my password for privileged tasks** if you want to avoid re-entering the password for every privileged operation.

### System Monitoring

The web console provides several tools to monitor system performance, including the **Utilization Saturation and Errors (USE) Method** for solving performance issues.

- **Utilization**: Represents the average time a resource was busy performing work.
- **Saturation**: Indicates how much extra work is queued and not being serviced.
- **Errors**: Refers to the number of recorded error events.

### Monitoring Performance in the Web Console

- Navigate to the **Overview** page and click **View details and history** to access performance metrics. The **Performance Metrics** page displays historical data, events, errors, and graphical representations for resource utilization and saturation.

### Reviewing Logs in the Web Console

- The **Logs** page in the web console provides access to journal logs using the `journalctl` service. You can filter log entries by:
  - **Time**: Define a time range for logs.
  - **Priority**: Set a minimum priority level (e.g., Error or Warning).
  - **Identifier**: Specify a `systemd` unit or service name.
  - **Text**: Enter keywords to search for specific log entries.

### Installing Add-ons for Enhanced Functionality

You can extend the web console's capabilities by installing various add-on packages:

| Feature             | Package                    | Usage                             |
|---------------------|----------------------------|-----------------------------------|
| Composer            | `cockpit-composer`         | Build custom OS images            |
| Package Management  | `cockpit-packagekit`       | Manage packages and install updates |
| Performance Metrics | `cockpit-pcp`              | Collect performance metrics       |
| Podman Containers   | `cockpit-podman`           | Manage Podman containers          |
| Session Recording   | `cockpit-session-recording`| Record and manage user sessions   |
| Storage Management  | `cockpit-storaged`         | Manage system storage             |
| Virtual Machines    | `cockpit-machines`         | Manage libvirt virtual machines   |

Install any add-on by using the `yum install` command:

```bash
[root@host ~]# yum install cockpit-<addon>
```

### Session Recording in the Web Console

The **Session Recording** feature uses the `tlog` package to record and play back user terminal sessions. This feature is useful for auditing purposes and allows administrators to view recorded session activity.

1. **Install the session recording add-on**:

   ```bash
   [root@host ~]# yum install cockpit-session-recording
   ```

2. **Configure the SSSD Service**

   - Ensure the `sssd` service is enabled and running:

   ```bash
   [root@host ~]# systemctl status sssd
   ```

3. **Configure Session Recording**

   - Edit the `/etc/sssd/conf.d/sssd-session-recording.conf` file to specify which users or groups to record. The available scope options are:
     - **none**: Record no sessions.
     - **some**: Record specific sessions.
     - **all**: Record all sessions.

   Example configuration:

   ```bash
   [session_recording]
   scope=none
   users=
   groups=
   ```

   - You can also configure session recording from the **Session Recording** page in the web console.

### Replay Recorded Sessions

1. **Play Back with the Web Console**
   - Use the **Session Recording** page to play back recorded sessions, complete with playback and analysis controls.

2. **Play Back with `tlog-play`**
   - From the command line, use the `tlog-play` command to replay recorded terminal sessions:

   ```bash
   [user@host ~]$ tlog-play --file-path=tlog.log
   ```

### References

- [Managing Systems Using the RHEL 9 Web Console Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/htmlsingle/managing_systems_using_the_rhel_8_web_console/index)
- [Recording Sessions Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/htmlsingle/recording_sessions/index)
- [Cockpit Project](http://www.cockpit-project.org)