IMPORTANT:
The ansible-galaxy command was originally written for ansible-playbook
users, for whom the control node and the automation execution environment
are the same system. The ansible-galaxy command installed files in the /
usr/share/ansible/collections directory on the control node so that the
ansible-playbook command could use them.
Red Hat Ansible Automation Platform 2 separates automation execution
environments from the control node so that they run in containers. These containerbased
automation execution environments have limited access to the control node's
file systems. When you run the playbook in the container, the collections_paths
directive uses the /usr/share/ansible/collections directory inside the
container, not on the control node. Therefore, if you install your collections in
the ~/.ansible/collections or /usr/share/ansible/collections
directories on the control node, the automation execution environment cannot use
them.
However, automation execution environments do have access to the directory
that hosts the playbook that they are running. If you install your collections in
a ./collections directory in the same directory as your playbook, then the
automation execution environment can access them.
Installing Collections with a Requirements File
The collections/requirements.yml file is a list of the collections needed to run playbooks
in your Ansible project, just like you can create a roles/requirements.yml file to list the roles
that you need to install. By adding a collections/requirements.yml file in your Ansible
project, your team members can immediately identify the required collections, and they can use
ansible-galaxy to manually install them. Automation controller detects the collections/
requirements.yml and automatically installs the collections it specifies before running your
playbooks.
The following collections/requirements.yml file lists several collections to install. You can
target a specific version of an Ansible Content Collection or provide local or remote .tar files or
Git repositories as the source of the collection.
---
collections:
- name: community.crypto
- name: ansible.posix
version: 1.2.0
- name: /tmp/community-dns-1.2.0.tar.gz
- name: http://www.example.com/redhat-insights-1.0.5.tar.gz
- name: git@github.com:ansible-collections/community.mysql.git
You can process that file with the ansible-galaxy command to install those collections. Use
the --requirements-file (or -r) option to provide the requirements.yml file to the
command.
80 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
[user@host ~]$ ansible-galaxy collection install -r requirements.yml
