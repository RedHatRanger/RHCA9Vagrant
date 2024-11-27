# Lesson 1: Improving Portability with Automation Execution Environments

## Introduction to Automation Execution Environments

Red Hat Ansible Automation Platform 2 introduces **automation execution environments**, a new feature aimed at improving consistency and portability in Ansible automation. Automation execution environments are container images that include Ansible Content Collections, their software dependencies, and a minimal Ansible engine for running playbooks. They simplify the development, testing, and deployment of automation code across **ansible-navigator** and **automation controller** (formerly known as Red Hat Ansible Tower).

Red Hat provides supported automation execution environments, which can also be customized. This approach eliminates the need for multiple Python virtual environments that were required in Red Hat Ansible Tower, making the overall process easier to manage.

## Benefits of Automation Execution Environments

- **Simplified Environment Management**: Automation execution environments replace the need for maintaining multiple Python virtual environments, which previously supported different combinations of Ansible, modules, Python libraries, and other dependencies.
- **Containerization**: Create customized environments in a **custom execution environment**, test it using **ansible-navigator**, and distribute it to the automation controller as a container image.
- **Consistency**: Provides a consistent environment for running automation tasks.

## Using Ansible Navigator with Automation Execution Environments

### Pulling the Default Execution Environment

By default, when using **ansible-navigator** to run a playbook without specifying an execution environment, the default automation execution environment will be pulled from **registry.redhat.io**. This environment contains **Ansible Core 2.13** and a set of **Red Hat Ansible Certified Content Collections**.

To authenticate and pull this image, use the following command:

```bash
podman login registry.redhat.io
```

Provide your Red Hat Customer Portal login credentials before running the **ansible-navigator** command.

> Note: In some classroom environments without internet access, you can download supported container images from **hub.lab.example.com**.

### Selecting Alternative Execution Environments

Use the `--execution-environment-image` (`--eei`) option to specify a different container image when running automation content. Tags can be used to specify versions, for example:

```bash
ansible-navigator --eei ee-supported-rhel8:latest
```

To control how **ansible-navigator** pulls container images, use the `--pull-policy` (`--pp`) option. Setting it to **missing** will only pull the image if it is not already present.

## Installing Automation Content Navigator

To install **ansible-navigator** on your control node, use a valid subscription to Red Hat Ansible Automation Platform:

1. Register your system:
   ```bash
   subscription-manager register
   ```
2. Enable the repository:
   ```bash
   subscription-manager repos \
   --enable ansible-automation-platform-2.2-for-rhel-8-x86_64-rpms
   ```
3. Install **ansible-navigator**:
   ```bash
   yum install ansible-navigator
   ```

> Note: You do not need to run these commands in the classroom lab environment.

## Configuring Authentication to Managed Hosts

To authenticate with managed hosts, **SSH key-based authentication** is recommended. You need to:

1. Set the **remote_user** directive in the Ansible configuration file on the control node.
2. Use **ssh-keygen** to create an SSH key pair for the user account that runs **ansible-navigator**.
3. Install the public key on the managed hosts using **ssh-copy-id**.
4. Configure **sudo** to allow privilege escalation without a password.

> Note: Unlike **ansible-playbook**, **ansible-navigator** runs playbooks inside a container that cannot directly access the **~/.ssh** directory. However, **ssh-agent** can provide SSH keys to the execution environment.

## Running Automation Content Navigator

Use **ansible-navigator** to run playbooks or explore Ansible features interactively:

- **Interactive Mode**: Run **ansible-navigator** with no arguments to start in interactive mode, which offers an interface for subcommands like **:config**, **:doc**, **:run**, etc.
- **Running Playbooks**: To run a playbook:
  ```bash
  ansible-navigator run <playbook.yml>
  ```
  The **run** command supports most options available with **ansible-playbook**.

### Reviewing Previous Playbook Runs

**Automation content navigator** offers a **replay** feature to display outputs from previous playbook runs. Replay files are saved by default and can be shared for troubleshooting:

```bash
ansible-navigator replay <artifact-file>
```

## Accessing Documentation

Use **ansible-navigator** to access documentation for plug-ins, such as modules, lookups, and callbacks. For example:

```bash
ansible-navigator doc ansible.posix.firewalld
```

> Note: Unlike **ansible-doc**, **ansible-navigator** requires explicit specification of the plug-in name.

## Getting Help

To get help while running **ansible-navigator** in interactive mode, type `:help` or use the `--help` (`-h`) option with the command to display syntax and options.

For additional help, run:

```bash
ansible-navigator run --help-playbook --mode stdout
```

