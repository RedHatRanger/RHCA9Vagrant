# Lab 6: Implementing Configuration Changes with an Ansible Playbook

## Objectives

- Configure a system with Ansible as a configuration manager.
- Install and configure the web service on a managed node using Ansible.

### Instructions

In this exercise, you will configure the workstation as an Ansible control node and install and configure the web service on the `node1` managed node.

1. **Create a working directory on the workstation**
   
   On the workstation, as the bob user, create a directory called `ansible` and change into it:
   
   ```bash
   [bob@workstation ~]$ mkdir ansible
   [bob@workstation ~]$ cd ansible
   ```

2. **Create the Ansible configuration file**
   
   Create an `ansible.cfg` configuration file with the following contents:
   
   ```ini
   [defaults]
   inventory = inventory
   remote_user = root
   host_key_checking = False
   deprecation_warnings = False
   ```

3. **Create the inventory file**
   
   Create an `inventory` file and set `node1` as the `web_prod` production web server:
   
   ```ini
   [webservers]
   web_prod ansible_host=node1
   ```

4. **Verify connectivity with the managed node**
   
   Verify that the control node can communicate with the managed node by using the `ping` module:
   
   ```bash
   [bob@ansible]$ ansible web_prod -m ping
   web_prod | SUCCESS => {
     "ansible_facts": {
       "discovered_interpreter_python": "/usr/libexec/platform-python"
     },
     "changed": false,
     "ping": "pong"
   }
   ```

5. **Move the downloaded templates to the ansible**
   
   Move the downloaded templates (`apache_httpdconf.j2` and `apache_indexhtml.j2`) to the `ansible` directory and view the included substitution variables:
   
   ```bash
   [bob@ansible]$ mv ~/apache_* .
   [bob@ansible]$ grep "{{" apache_httpdconf.j2
   # {{ ansible_managed }}
   [bob@ansible]$ grep "{{" apache_indexhtml.j2
   <!-- {{ ansible_managed }} -->
   Hello from {{ inventory_hostname }}
   ```

6. **Create the Ansible Playbook for web service configuration**
   
   Create an Ansible playbook called `mywebserver.yaml` to include the following tasks:

   - Install the `httpd` package if not installed.
   - Use the templates as the web service configuration files.
   - Ensure that the firewall service is enabled for the web service.

   #### Playbook Contents:
   ```bash
   vim mywebserver.yaml
   ```

   ```yaml
   - name: Install and configure a customized web server
     hosts: webservers
     gather_facts: False
     tasks:
       - name: Install httpd package
         ansible.builtin.yum:
           name:
             - httpd
             - firewalld
           state: latest
   
       - name: Template out httpd configuration file
         template:
           src: apache_httpdconf.j2
           dest: /etc/httpd/conf/httpd.conf
           owner: root
           group: root
           mode: '0444'

       - name: Template out httpd index file
         template:
           src: apache_indexhtml.j2
           dest: /var/www/html/index.html
           owner: root
           group: root
           mode: '0444'

       - name: Start and enable httpd daemon
         ansible.builtin.service:
           name: httpd
           state: started
           enabled: true

       - name: Start and enable firewalld daemon
         ansible.builtin.service:
           name: firewalld
           state: started
           enabled: true

       - name: Open http firewalld port
         firewalld:
           service: http
           immediate: yes
           permanent: yes
           state: enabled
   ```

   **Note**: You can verify the syntax of the playbook without implementing it by using:

   ```bash
   [bob@ansible]$ ansible-playbook --syntax-check mywebserver.yaml
   playbook: mywebserver.yaml
   ```

8. **Run the Ansible Playbook**

   Run the playbook and verify its successful execution:

   ```bash
   [bob@ansible]$ ansible-playbook mywebserver.yaml
   
   PLAY [Install and configure a customized web server] *********************
   TASK [Install httpd package] *********************************************
   changed: [web_prod]
   TASK [Validate firewall] *************************************************
   ok: [web_prod]
   TASK [Template out httpd configuration file] *****************************
   changed: [web_prod]
   TASK [Template out httpd index file] *************************************
   ok: [web_prod]
   TASK [Start and enable httpd daemon] *************************************
   changed: [web_prod]
   TASK [Start and enable firewalld daemon] *********************************
   ok: [web_prod]
   TASK [Open http firewalld port] ******************************************
   changed: [web_prod]
   
   PLAY RECAP ***************************************************************
   web_prod : ok=7 changed=5 unreachable=0 failed=0 skipped=0
   rescued=0 ignored=0
   ```

9. **Verify the web server**

   Verify that the web server can be accessed:

   ```bash
   [bob@ansible]$ curl node1
   <!-- ansible managed -->
   <html>
   <head><title>Apache is running!</title></head>
   <body>
   <h1>
   Hello from web_prod
   </h1>
   </body>
   </html>
   ```

* Done!!
