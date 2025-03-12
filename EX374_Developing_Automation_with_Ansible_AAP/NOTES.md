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

81-82
You can also use the ansible-galaxy collection list command to list the collections
available in the directories specified by your current collections_paths Ansible configuration
directive on your control node. It does not list collections that you might have installed in
directories not specified by that directive.
[user@host ~]$ ansible-galaxy collection list
# /home/user/.ansible/collections/ansible_collections
Collection Version
---------------- ------
ansible.posix 1.3.0
community.crypto 1.9.5
community.dns 1.2.0
community.mysql 2.1.0
redhat.insights 1.0.7

The ansible-galaxy collection list command lists the Ansible Content
Collections installed in the directory specified by the collections_paths
directive on the control node. This might include collections installed in your Ansible
project directory, if that location is listed in your collections_paths directive.
You must use the ansible-navigator collections command to list the
Ansible Content Collections in your current automation execution environment. This
does not include any collections that your Ansible project brings into the automation
execution environment when a playbook in the project is run.
Neither command lists the ansible.builtin collection, which is always available.

84-85
Installing Collections from Private Automation Hub
Configuring ansible-galaxy to get content from a private automation hub is similar to how it is
configured to use automation hub. The following example includes a new section that configures
ansible-galaxy to use a private automation hub installed on hub.example.com.
...output omitted...
[galaxy]
server_list = my_hub, automation_hub, galaxy
[galaxy_server.my_hub]
url=https://hub.example.com/api/galaxy/content/rh-certified/
token=e8e4...b0c2
[galaxy_server.automation_hub]
url=https://console.redhat.com/api/automation-hub/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openidconnect/
token
[galaxy_server.galaxy]
url=https://galaxy.ansible.com/
Notice that you do not need the auth_url directive for private automation hub.
Use the private automation hub web UI to retrieve the URL and generate the token.
Navigate to Collections > Repository Management to retrieve the URL. Private automation hub
can host collections that you have synchronized from automation hub, from Ansible Galaxy, or that
you have created for your teams. Select the URL corresponding to the type of collection you want
to install.

86
The community repository uses the collections that you have synchronized from Ansible
Galaxy.
The published repository uses custom collections that you have installed and approved.
The rh-certified repository uses the collections that you have synchronized from automation
hub.
To generate a token, either click Get token on the Repo Management page or navigate to
Collections > API token management and then click Load token.
Installing and deploying private automation hub is beyond the scope of this course.

87
Instructions
1. Browse the Ansible Content Collections available from the classroom's private automation
hub. Access the details for one of the collections that Red Hat provides and supports,
ansible.netcommon.
1.1. Use a browser to navigate to the private automation hub at https://
hub.lab.example.com, and then log in as student using redhat123 as the
password.
1.2. Navigate to Collections > Collections to browse the Ansible Content Collections.
Because the ansible.netcommon collection is a Red Hat Certified Ansible Content
Collection, select Red Hat Certified from the Filter by repository menu.
1.3. Search for the collection by entering netcommon in the search field. When the search
completes, click the netcommon tile.
1.4. Notice that the UI displays an ansible-galaxy collection install
ansible.netcommon command that you can use to install the collection. Click the
Documentation tab to access the list of modules and plug-ins available with this
collection.
2. On the workstation machine, configure the Ansible project in the /home/student/
manage-finding/ directory so that the ansible-galaxy command retrieves
collections from the classroom's private automation hub. Enable access to the rhcertified
and community repositories.
DO374-RHAAP2.2-en-1-20230131 87
Chapter 2 | Managing Content Collections and Execution Environments
2.1. Change to the /home/student/manage-finding/ directory.
[student@workstation ~]$ cd ~/manage-finding/
Important
You need to run the ansible-galaxy commands in this exercise from the /home/
student/manage-finding/ directory, which contains the Ansible project. This
helps ensure that you use the correct configuration file and install Ansible Content
Collections to the correct location.
2.2. From the private automation hub web UI, navigate to Collections > Repository
Management. This page has the parameters that you need for configuring the
ansible.cfg file.
Click the Copy to clipboard icon in the CLI configuration column and the rh-certified
row.
Do not close your web browser window.
Note
You might need to collapse the web UI navigation bar or zoom out in your web
browser to see the Copy to clipboard icon for the CLI configuration column.
2.3. Modify the ansible.cfg file to append the lines that you copied to the clipboard
in the preceding step. The modified ansible.cfg file now contains the following
content:
[defaults]
inventory = ./inventory
[galaxy]
server_list = rh-certified_repo
[galaxy_server.rh-certified_repo]
url=https://hub.lab.example.com/api/galaxy/content/rh-certified/
token=<put your token here>
2.4. Return to the Repo Management page in the private automation hub web UI. Click
the Copy to clipboard icon in the CLI configuration column and the community row.
Do not close your web browser window.
2.5. Modify the ansible.cfg file to append the lines that you copied to the clipboard
in the preceding step. The modified ansible.cfg file now contains the following
content:
[defaults]
inventory = ./inventory
[galaxy]
server_list = rh-certified_repo
88 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
[galaxy_server.rh-certified_repo]
url=https://hub.lab.example.com/api/galaxy/content/rh-certified/
token=<put your token here>
[galaxy]
server_list = community_repo
[galaxy_server.community_repo]
url=https://hub.lab.example.com/api/galaxy/content/community/
token=<put your token here>
You added a second [galaxy] section to the file, and there needs to be only one
[galaxy] section. You correct that problem in the next step.
2.6. Update the ansible.cfg file so that it only contains one [galaxy] section.
Update the first [galaxy] section so that it lists both repositories, rhcertified_
repo and community_repo. Remove the second [galaxy] section.
The modified ansible.cfg file now contains the following content.
[defaults]
inventory = ./inventory
[galaxy]
server_list = rh-certified_repo, community_repo
[galaxy_server.rh-certified_repo]
url=https://hub.lab.example.com/api/galaxy/content/rh-certified/
token=<put your token here>
[galaxy_server.community_repo]
url=https://hub.lab.example.com/api/galaxy/content/community/
token=<put your token here>
2.7. Return to the private automation hub web UI. Navigate to Collections > API token
management and then click Load token. Copy the API token.
2.8. Using the copied token, update both token lines in the ansible.cfg file. Your
token is probably different from the one displayed in this example. Save and close the
file when done.
[defaults]
inventory = ./inventory
[galaxy]
server_list = rh-certified_repo, community_repo
[galaxy_server.rh-certified_repo]
url=https://hub.lab.example.com/api/galaxy/content/rh-certified/
token=c4f21af7090f0f9cb74d3c3f9e1748884ecdc180
[galaxy_server.community_repo]
url=https://hub.lab.example.com/api/galaxy/content/community/
token=c4f21af7090f0f9cb74d3c3f9e1748884ecdc180
DO374-RHAAP2.2-en-1-20230131 89
Chapter 2 | Managing Content Collections and Execution Environments
3. Create a collections directory within the project directory, and then install the
ansible.netcommon collection into the new directory.
3.1. Create the collections directory.
[student@workstation manage-finding]$ mkdir collections
3.2. Use the ansible-galaxy command to install the ansible.netcommon collection
into the collections directory.
[student@workstation manage-finding]$ ansible-galaxy collection install \
> ansible.netcommon -p collections/
Starting galaxy collection install process
[WARNING]: The specified collections path '/home/student/manage-finding/
collections' is not part of the configured Ansible
collections paths '/home/student/.ansible/collections:/usr/share/ansible/
collections'. The installed collection won't be picked up
in an Ansible run.
Process install dependency map
Starting collection install process
Downloading https://hub.lab.example.com/api/galaxy/v3/plugin/ansible/content/
rh-certified/collections/artifacts/ansible-netcommon-4.1.0.tar.gz to /
home/student/.ansible/tmp/ansible-local-29962sr162r7w/tmprxsyau18/ansiblenetcommon-
4.1.0-eduihb5k
[WARNING]: The GnuPG keyring used for collection signature verification was not
configured but signatures were provided by the
Galaxy server to verify authenticity. Configure a keyring for ansible-galaxy to
use or disable signature verification. Skipping
signature verification.
Installing 'ansible.netcommon:4.1.0' to '/home/student/manage-finding/collections/
ansible_collections/ansible/netcommon'
Downloading https://hub.lab.example.com/api/galaxy/v3/plugin/ansible/content/
rh-certified/collections/artifacts/ansible-utils-2.8.0.tar.gz to /home/
student/.ansible/tmp/ansible-local-29962sr162r7w/tmprxsyau18/ansible-utils-2.8.0-
hn1_8a18
ansible.netcommon:4.1.0 was installed successfully
Installing 'ansible.utils:2.8.0' to '/home/student/manage-finding/collections/
ansible_collections/ansible/utils'
ansible.utils:2.8.0 was installed successfully
Note
You might see two WARNING messages in the output that you can safely ignore.
The first one is solved when you configure the collections_paths directive
in the next step. The second warning occurs because the Ansible Galaxy server
is providing GnuPG digital signatures that can be used to verify the downloaded
content, but your client is not configured to validate those signatures.
The preceding output indicates that the ansible-galaxy command downloaded
the collection from https://hub.lab.example.com, the classroom private
automation hub.
90 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
Notice that the ansible.utils collection is installed automatically as a
dependency of the ansible.netcommon collection.
3.3. Modify the ansible.cfg file so that it explicitly looks for collections in the project
collections directory. The top of the modified file now contains the following
content:
[defaults]
inventory = ./inventory
collections_paths = ./collections:/usr/share/ansible/collections
...output omitted...
3.4. In the /home/student/manage-finding directory, use the ansible-galaxy
command to list the collections installed on the control node that are specified by the
current collections_paths directive.
[student@workstation manage-finding]$ ansible-galaxy collection list
# /home/student/manage-finding/collections/ansible_collections
Collection Version
----------------- -------
ansible.netcommon 4.1.0
ansible.utils 2.8.0
# /usr/share/ansible/collections/ansible_collections
Collection Version
------------------------ -------
redhat.rhel_system_roles 1.16.2
Note
Although other Ansible Content Collections might be installed on the system, the
ansible-galaxy command only lists collections in the directories specified by
the collections_paths directive. The versions of the ansible.netcommon,
ansible.utils, and redhat.rhel_system_roles collections installed on your
system might be different than the versions displayed here.
4. Install version 2.1.0 of the redhat.rhv collection. Make sure that you run ansiblegalaxy
from a prompt in the Ansible project's /home/student/manage-finding
directory. Because you updated the project's ansible.cfg file to look first for collections
in the ./collections directory, you do not need to use the -p option.
[student@workstation manage-finding]$ ansible-galaxy collection install \
> redhat.rhv:2.1.0
...output omitted...
Installing 'redhat.rhv:2.1.0' to '/home/student/manage-finding/collections/
ansible_collections/redhat/rhv'
redhat.rhv:2.1.0 was installed successfully
5. Install collections specified in a requirements file. Include the existing
ansible.netcommon and redhat.rhv collections, and then add two additional
collections, community.crypto and ansible.controller.
DO374-RHAAP2.2-en-1-20230131 91
Chapter 2 | Managing Content Collections and Execution Environments
5.1. Create a file named requirements.yml in the collections directory, and add
the following content:
---
collections:
- name: ansible.netcommon
- name: redhat.rhv
version: 2.1.0
- name: community.crypto
version: 2.7.0
- name: ansible.controller
version: 4.2.1
5.2. Save and close the file when done.
5.3. In the /home/student/manage-finding directory, install the collections by using
the ansible-galaxy command. Add the -r option to specify the location of the
requirements file.
When you run the command, it skips installing the ansible.netcommon,
redhat.rhv, and ansible.utils collections because they are already installed.
[student@workstation manage-finding]$ ansible-galaxy collection install \
> -r collections/requirements.yml
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Downloading https://hub.lab.example.com/api/galaxy/v3/plugin/ansible/content/
community/collections/artifacts/community-crypto-2.7.0.tar.gz to /home/
student/.ansible/tmp/ansible-local-32771_upchxep/tmp36d_hwh7/communitycrypto-
2.7.0-fx5pgw7w
'ansible.netcommon:4.1.0' is already installed, skipping.
'redhat.rhv:2.1.0' is already installed, skipping.
'ansible.utils:2.8.0' is already installed, skipping.
Installing 'community.crypto:2.7.0' to '/home/student/manage-finding/collections/
ansible_collections/community/crypto'
Downloading https://hub.lab.example.com/api/galaxy/v3/plugin/ansible/content/
rh-certified/collections/artifacts/ansible-controller-4.2.1.tar.gz to /
home/student/.ansible/tmp/ansible-local-32771_upchxep/tmp36d_hwh7/ansiblecontroller-
4.2.1-89anvds8
community.crypto:2.7.0 was installed successfully
[WARNING]: The GnuPG keyring used for collection signature verification was not
configured but signatures were provided by the
Galaxy server to verify authenticity. Configure a keyring for ansible-galaxy to
use or disable signature verification. Skipping
signature verification.
Installing 'ansible.controller:4.2.1' to '/home/student/manage-finding/
collections/ansible_collections/ansible/controller'
ansible.controller:4.2.1 was installed successfully
92 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
5.4. In the Ansible project's /home/student/manage-finding directory, list the
collections installed on the control node.
[student@workstation manage-finding]$ ansible-galaxy collection list
# /home/student/manage-finding/collections/ansible_collections
Collection Version
------------------ -------
ansible.controller 4.2.1
ansible.netcommon 4.1.0
ansible.utils 2.8.0
community.crypto 2.7.0
redhat.rhv 2.1.0
# /usr/share/ansible/collections/ansible_collections
Collection Version
------------------------ -------
redhat.rhel_system_roles 1.16.2
Finish
On the workstation machine, change to the student user home directory and use the lab
command to complete this exercise. This step is important to ensure that resources from previous
exercises do not impact upcoming exercises.

94
Selecting an Execution Environment
Objectives
Identify the automation execution environments provided by Red Hat and select the correct one
for your use case.
Automation Execution Environments
An automation execution environment is a container image that includes Ansible Content
Collections, their software dependencies, and a minimal Ansible engine that can run your
playbooks.
By using an automation execution environment, you can use the same portable environment to
develop your Ansible Playbooks on one system and run them on another. This streamlines and
simplifies the development process and helps to ensure predictable, reproducible results.
The automation execution environment is where your Ansible Playbook runs. You normally use a
tool such as ansible-navigator to run a playbook, but the playbook runs inside the container
rather than directly on your system.
An automation execution environment consists of the following components:
• Ansible Core (or Ansible)
• Ansible Content Collections to supplement Ansible Core
• Python and any other dependencies of Ansible Core and the included collections
• Ansible Runner to run your playbooks
Automation execution environments are prepared by the execution environment builder
(ansible-builder), and can be pushed to any container registry.

95
The minimal automation execution environment only includes the ansible.builtin Ansible
Content Collection. You usually reserve it as a starting point to build custom automation execution
environments.
The supported automation execution environment includes some collections that Red Hat
supports. Automation content navigator and automation controller use the supported automation
execution environment by default. Use this automation execution environment with your new
playbooks.
Inspecting Automation Execution Environments
Use automation content navigator to list and inspect the automation execution environments
available on the system. To do so, run the ansible-navigator images command:
DO374-RHAAP2.2-en-1-20230131 95
Chapter 2 | Managing Content Collections and Execution Environments
[user@host ~]$ ansible-navigator images

96-97
Using Automation Execution Environments with
Automation Content Navigator
The ansible-navigator run command runs your playbooks in an automation execution
environment.
This command uses the supported automation execution environment by default, but you can use
the --execution-environment-image (or --eei) option to specify a different environment.
The following example shows how to run a playbook using the compatibility automation execution
environment:
[user@host ~]$ ansible-navigator run oldplaybook.yml --eei \
> registry.redhat.io/ansible-automation-platform-22/ee-29-rhel8:latest
If the container image is already available on your system, then you can use its short image name:
--eei ee-29-rhel8:latest in the preceding example.
If the container image is not already available on your system, ansible-navigator tries to
pull it from the container registry. Use the podman login command to ensure that you are
authenticated to the registry. In the preceding example, at the beginning of your session, run the
podman login registry.redhat.io command and provide your Customer Portal credentials
to authenticate to the registry.
You can control how ansible-navigator pulls container images by using the --pull-policy
(or --pp) option with one of the following arguments:
96 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
Option Description
always Always pulls the image.
missing Only pulls the image if it is not already available locally.
never Never pulls the image.
tag Pulls the image if the image tag is latest or if it is not already available
locally (the default).
Note
Instead of using the --eei option, you can create an ansible-navigator.yml
configuration file to define the automation execution environment to use by default.
A later chapter describes that configuration file in more detail.

98-102 LAB
Guided Exercise
Selecting an Execution Environment
In this exercise, you ensure that the execution environments you need are installed on your
development workstation and use them to run playbooks.
Outcomes
• Identify the automation execution environment that includes a specific Ansible Content
Collection.
• Run a playbook using that automation execution environment.
Before You Begin
As the student user on the workstation machine, use the lab command to prepare your
system for this exercise.
This command creates an Ansible project in the /home/student/manage-selecting/
directory.
[student@workstation ~]$ lab start manage-selecting
Instructions
1. Identify the Ansible Content Collection that is required to run the prebuilt playbook.
1.1. From a terminal, change to the /home/student/manage-selecting/ directory
and review the acl_info.yml playbook.
[student@workstation ~]$ cd ~/manage-selecting
[student@workstation manage-selecting]$ cat acl_info.yml
---
- name: Retrieving ACLs using the ansible.posix collection
hosts: web_servers
gather_facts: false
tasks:
- name: Obtain the ACLs for /webserver_data directory
ansible.posix.acl:
path: /webserver_data/
register: acl_info
- name: Print the ACLs obtained for /webserver_data directory
ansible.builtin.debug:
var: acl_info['acl']
1.2. Identify the modules that the acl_info.yml playbook uses:
• ansible.posix.acl
98 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
• ansible.builtin.debug
To successfully run the playbook, you need the ansible.posix.acl module, which
is part of the ansible.posix Ansible Content Collection.
Note
The ansible.builtin.debug module is part of the ansible.builtin
collection that is included in the ansible-core RPM package.
2. Find the automation execution environment that includes the ansible.posix Ansible
Content Collection.
2.1. Use the podman login command to log in to the classroom private automation hub
at hub.lab.example.com.
This step simulates logging in to registry.redhat.io, but uses the private
automation hub in the classroom environment. That private automation hub
includes the three automation execution environments provided by Red Hat Ansible
Automation Platform 2.
[student@workstation manage-selecting]$ podman login hub.lab.example.com
Username: student
Password: redhat123
Login Succeeded!
2.2. Use the ansible-navigator images command to pull the minimal automation
execution environment and analyze it.
[student@workstation manage-selecting]$ ansible-navigator images \
> --eei hub.lab.example.com/ee-minimal-rhel8
------------------------------------------------------------------------------
Execution environment image and pull policy overview
------------------------------------------------------------------------------
Execution environment image name: hub.lab.example.com/ee-minimal-rhel8:latest
Execution environment image tag: latest
Execution environment pull arguments: None
Execution environment pull policy: tag
Execution environment pull needed: True
...output omitted...
After a few seconds, automation content navigator displays output that lists the eeminimal-
rhel8 automation execution environment.
Image Tag Execution environment ...
0|ee-minimal-rhel8 latest True ...
2.3. Type 0 to inspect this automation execution environment, and then type 2 to select
the Ansible version and collections option.
DO374-RHAAP2.2-en-1-20230131 99
Chapter 2 | Managing Content Collections and Execution Environments
Image: ee-minimal-rhel8:latest (primary) Description
0│Image information Information collected from image...
1│General information OS and python version information
2│Ansible version and collections Information about ansible and an...
3│Python packages Information about python and pyt...
4│Operating system packages Information about operating syst...
5│Everything All image information
2.4. The resulting output indicates that the minimal automation execution environment
does not contain any additional Ansible Content Collections.
Image: ee-minimal-rhel8:latest (primary) (Information about ansible and ...
0│---
1│ansible:
2│ collections:
3│ details: {}
4| errors:
5| - |-
6| ERROR! - None of the provided paths were usable. Please specify a valid ...
7| version:
8| details: ansible [core 2.13.4]
Type :q and then press Enter to exit the ansible-navigator command. The eeminimal-
rhel8 automation execution environment is not useful for running this
playbook.
2.5. Use the ansible-navigator command to pull down and inspect the supported
automation execution environment.
[student@workstation manage-selecting]$ ansible-navigator images \
> --eei hub.lab.example.com/ee-supported-rhel8
--------------------------------------------------------------------------------
Execution environment image and pull policy overview
--------------------------------------------------------------------------------
Execution environment image name: hub.lab.example.com/ee-supported-rhel8:...
Execution environment image tag: latest
Execution environment pull arguments: None
Execution environment pull policy: tag
Execution environment pull needed: True
...output omitted...
2.6. Automation content navigator shows both automation execution environments.
Type 1 to inspect the supported one and then type the number for the Ansible
version and collections entry.
NAME Tag Execution environment ...
0|ee-minimal-rhel8 latest True ...
1|ee-supported-rhel8 latest True ...
2.7. The resulting output indicates that the supported automation execution environment
contains the ansible.posix Ansible Content Collection.
100 DO374-RHAAP2.2-en-1-20230131
Chapter 2 | Managing Content Collections and Execution Environments
Image: ee-supported-rhel8:latest (primary) (Information about ansible and ...
0│---
1│ansible:
2│ collections:
3│ details:
4| amazon.aws: 3.2.0
5│ ansible.controller: 4.2.1
6│ ansible.netcommon: 3.1.1
7│ ansible.network: 1.2.0
8│ ansible.posix: 1.3.0
9│ ansible.security: 1.0.0
...output omitted...
2.8. Type :q and then press Enter to exit the ansible-navigator command.
The ee-supported-rhel8 automation execution environment contains the
ansible.posix Ansible Content Collection that you need. You can run this
playbook using this automation execution environment.
3. Use automation content navigator to run the acl_info.yml playbook.
3.1. Demonstrate that the minimal automation execution environment does not work
with the acl_info.yml playbook because it does not include the ansible.posix
collection. Use the ansible-navigator command to run the acl_info.yml
playbook using the minimal automation execution environment.
As expected, the playbook cannot resolve the ansible.posix.acl module,
causing it to fail.
[student@workstation manage-selecting]$ ansible-navigator run acl_info.yml \
> -m stdout --eei ee-minimal-rhel8 --pp missing
ERROR! couldn't resolve module/action 'ansible.posix.acl'. This often indicates a
misspelling, missing collection, or incorrect module path.
The error appears to be in '/home/student/manage-selecting/acl_info.yml': line 7,
column 7, but may be elsewhere in the file depending on the exact syntax problem.
The offending line appears to be:
tasks:
- name: Obtain the ACLs for /webserver_data directory
^ here
3.2. Use the ansible-navigator command to run the acl_info.yml playbook
using the ee-supported-rhel8:latest automation execution environment. This
automation execution environment contains the ansible.posix collection and the
command succeeds.
[student@workstation manage-selecting]$ ansible-navigator run acl_info.yml \
> -m stdout --eei ee-supported-rhel8 --pp missing
PLAY [Retrieving ACLs using the ansible.posix collection] **********************
TASK [Obtain the ACLs for /webserver_data directory] ***************************
DO374-RHAAP2.2-en-1-20230131 101
Chapter 2 | Managing Content Collections and Execution Environments
ok: [serverc.lab.example.com]
ok: [serverb.lab.example.com]
TASK [Print the ACLs obtained for /webserver_data directory] *******************
ok: [serverc.lab.example.com] => {
"acl_info['acl']": [
"user::rwx",
"group::r-x",
"other::r-x",
"default:user::rwx",
"default:group::r-x",
"default:mask::rwx",
"default:other::r-x"
]
}
ok: [serverb.lab.example.com] => {
"acl_info['acl']": [
"user::rwx",
"group::r-x",
"other::r-x",
"default:user::rwx",
"default:group::r-x",
"default:mask::rwx",
"default:other::r-x"
]
}
PLAY RECAP *********************************************************************
serverb.lab.example.com : ok=2 changed=0 unreachable=0 failed=0 ...
serverc.lab.example.com : ok=2 changed=0 unreachable=0 failed=0 ...
Finish
On the workstation machine, change to the student user home directory and use the lab
command to complete this exercise. This step is important to ensure that resources from previous
exercises do not impact upcoming exercises.
