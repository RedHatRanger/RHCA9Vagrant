## **Lab 1: Setting Up the Environment**

### Objective
Prepare the environment for the labs, including setting up Vagrant nodes.

### Steps
1. **Provision the VMs**:
   - Run the Vagrantfile:
     ```bash
     vagrant up
     ```
   - Ensure all nodes come up successfully.

2. **Verify PostgreSQL Setup**:
   - SSH into the database node:
     ```bash
     vagrant ssh db
     ```
   - Verify PostgreSQL is running:
     ```bash
     sudo systemctl status postgresql
     ```
   - Test local access:
     ```bash
     sudo -u postgres psql -c "\l"
     ```

3. **Verify Connectivity Between Nodes**:
   - From `controller.local`, test connectivity to the `db.local` and `pah.local` nodes:
     ```bash
     ping db.local
     ping pah.local
     ```

4. **Test Ansible Connectivity**:
   - SSH into the `controller` node:
     ```bash
     vagrant ssh controller
     ```
   - Test Ansible ping for all nodes:
     ```bash
     ansible all -i "/vagrant/inventory" -m ping
     ```

5. **Verify GitLab**:
   - Access GitLab at `http://192.168.56.12` and confirm the service is running.

---


















## **Lab 8: Using the Automation Controller API**

### Objective
Use the API for advanced automation tasks.

### Steps
1. **Retrieve API Token**:
   - Log in to the Controller and navigate to **Token Management**.

2. **Test API Endpoints**:
   - Use `curl` or Postman to interact with the API:
     ```bash
     curl -X GET -H "Authorization: Bearer <token>" https://controller.local/api/v2/
     ```

3. **Automate Job Launch**:
   - Write a script to launch a job template using the API.

---

## **Lab 9: Backup and Recovery**

### Objective
Backup and restore AAP components.

### Steps
1. **Backup Controller**:
   - Use the `ansible-callbacks` CLI to back up configurations.

2. **Backup PAH**:
   - Archive PAH data and database dumps.

3. **Simulate Restore**:
   - Restore the Controller and PAH from backups.

---

## **Lab 10: Configuring Automation Mesh**

### Objective
Install and configure Automation Mesh for distributed execution.

### Steps
1. **Install Automation Mesh Nodes**:
   - Deploy additional nodes configured as execution nodes.
   - Ensure they are reachable from the Controller.

2. **Create Instance Groups**:
   - Navigate to **Instance Groups** > **Create Instance Group**.
   - Assign the Automation Mesh nodes to the group.

3. **Test Job Execution**:
   - Assign a job template to the instance group and execute it.

---

Would you like me to continue with more labs?

