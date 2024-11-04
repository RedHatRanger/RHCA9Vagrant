
# Lesson 6: Describing Configuration Management Automation

## Objectives

- Understand the use of configuration management with Red Hat Satellite and Red Hat Ansible Automation Platform.

### Configuration Management Solutions

As an organization's IT infrastructure grows, manual system configuration can become unmanageable, introducing human errors, scalability issues, and potential instability. To address these issues, configuration management tools are used to automate and manage system configurations effectively, enhancing stability, efficiency, and reducing the risk of human error.

### Red Hat Satellite Server

*Red Hat Satellite* is a system management solution that helps deploy, configure, and maintain systems across physical, virtual, and cloud environments. It centralizes provisioning, remote management, and monitoring of multiple Red Hat Enterprise Linux deployments. Satellite Server organizes lifecycle management through the concept of "organizations," which isolate content for different groups of hosts with specific needs.

### Red Hat Ansible Automation Platform

*Ansible Automation Platform* is a simple, agentless automation language used to define IT infrastructure through playbooks. It pushes configurations to designated remote systems using SSH and provides scalability to large environments.

Ansible Automation Platform also includes an automation controller (formerly known as Red Hat Ansible Tower), which provides a dashboard and graphical tools for managing automation. The automation controller can integrate with Red Hat Satellite to manage server inventory and host playbooks for configuration tasks.

### Using Ansible Automation Platform for Configuration Management

Ansible Automation Platform enables system administrators to implement Infrastructure as Code (IaC) by defining system configurations programmatically, instead of manually configuring systems. It uses YAML syntax to write playbooks, making it straightforward to read and write automation instructions.

Playbooks can configure remote systems, sequence multi-tier rollouts, delegate tasks, and manage configuration across multiple hosts. The agentless nature of Ansible Automation Platform makes it easy to set up, with communication happening over SSH using user accounts with appropriate privileges.

### Ansible Automation Platform Architecture

- **Control Node**: A host where Ansible Automation Platform is installed, and from which playbooks are launched.
- **Managed Node**: Hosts managed by the control node, which execute tasks specified in playbooks.

The control node stores several important files, including:

- `ansible.cfg`: Configuration file for Ansible Automation Platform.
- Inventory: A list of managed nodes.
- Playbooks, modules, or roles: Files that define configuration tasks.

### Customized Configuration

Ansible Playbooks can dynamically retrieve or discover variables called "facts" from managed nodes. These facts influence configuration behavior and provide flexibility for managing systems. For example, an IP address discovered on one system can be used as a service configuration value on another system.

Ansible uses Jinja2 templates for configuration files, enabling dynamic variables and expressions to be substituted, such as:

```yaml
- name: Template example configuration file
  template:
    src: file.cfg.j2
    dest: {{ remote_install_path }}/file.cfg
```

The *remote_install_path* variable defines a file location, which might vary from one system to
another.

A playbook can disable fact gathering, with this syntax:
```yaml
- hosts: servers
 gather_facts: no
```
### RHEL System Roles

Red Hat Enterprise Linux (RHEL) includes a collection of Ansible roles called RHEL System Roles to manage system configurations across different versions of RHEL. These roles are consistent and can be used for troubleshooting and maintaining desired configurations. Some of the key RHEL System Roles include:

- **certificate**: Manages TLS/SSL certificates.
- **crypto_policies**: Manages system-wide cryptographic policies.
- **ha_cluster**: Manages High Availability (Corosync) clusters.
- **logging**: Configures system logging with rsyslog.
- **network**: Configures network interfaces such as Ethernet, VLAN, and Wireless (WiFi).
- **selinux**: Manages SELinux settings, Booleans, and file contexts.
- **timesync**: Configures time synchronization using NTP or PTP.
- **tlog**: Configures terminal session recording using tlog.

To use RHEL System Roles, the `rhel-system-roles` package must be installed on the control node:

```bash
[root@host ~]# yum install rhel-system-roles
```

To implement roles in a playbook:

```yaml
- hosts: servers
  roles:
    - rhel-system-roles.network
    - rhel-system-roles.timesync
```

The roles are stored in `/usr/share/ansible/roles`, and each role includes documentation for use cases, parameters, and templates, available in both Markdown and HTML formats.

![{0592F3A8-6A64-414D-8023-AEE38F521957}](https://github.com/user-attachments/assets/1a9078a5-a8b2-4240-a528-d085774e0a80) \

The README file describes the role's use cases, parameters, templates, example syntax, and
structure. To view the HTML version, use your browser to open the file from the command line:
[user@host ~]# cd /usr/share/ansible/roles
[user@host roles]# firefox linux-system-roles.certificate/README.html

![{F9429909-A110-4C0F-8A86-10DDC045251C}](https://github.com/user-attachments/assets/0fd58efc-73a6-4a39-bbda-08ce28039110)


