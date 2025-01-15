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










## **Lab 4: Configuring Private Automation Hub**

### Objective
Configure PAH and manage content.

### Steps
1. **Log In to PAH**:
   - URL: `https://192.168.56.11`
   - Username: `admin`
   - Password: `redhat`

2. **Sync Collections**:
   - Navigate to **Repositories** > **Collections**.
   - Configure syncing from Red Hat Automation Hub (if online).

3. **Upload Custom Collections**:
   - Use the `ansible-galaxy` CLI to upload a collection:
     ```bash
     ansible-galaxy collection publish my-collection.tar.gz --api-server=https://192.168.56.11
     ```

4. **Upload Execution Environments**:
   - Build and push an EE:
     ```bash
     podman build -t my-ee:latest .
     podman login https://192.168.56.11
     podman push my-ee:latest https://192.168.56.11/my-ee:latest
     ```

5. **Manage Access**:
   - Configure roles and permissions for PAH users.

---

## **Lab 5: Managing Inventories and Credentials**

### Objective
Manage inventory and credentials in the Controller.

### Steps
1. **Create an Inventory**:
   - Log in to the Controller.
   - Navigate to **Inventories** > **Create Inventory**.
   - Provide a name (e.g., `Production Inventory`) and organization.

2. **Add Hosts**:
   - Open the inventory and select **Hosts** > **Add Host**.
   - Add the Worker node (e.g., `worker.local`) and its variables.

3. **Create Machine Credentials**:
   - Navigate to **Credentials** > **Create Credential**.
   - Set the type to **Machine** and provide the username (`rhel`) and password (`redhat`).

4. **Test Connectivity**:
   - Create a Job Template to ping the inventory:
     ```bash
     ansible -i /path/to/inventory all -m ping
     ```

---

## **Lab 6: Managing Projects and Templates**

### Objective
Set up and manage automation projects and job templates.

### Steps
1. **Create a Project**:
   - Navigate to **Projects** > **Create Project**.
   - Link the project to the GitLab repository created earlier.

2. **Create a Job Template**:
   - Navigate to **Templates** > **Create Template**.
   - Associate the template with the created project and inventory.

3. **Run the Template**:
   - Launch the job template and verify the results.

4. **Schedule Jobs**:
   - Navigate to **Schedules** and create a schedule for the job template.

---

## **Lab 7: Working with Workflows**

### Objective
Create and manage workflow templates.

### Steps
1. **Create a Workflow Template**:
   - Navigate to **Templates** > **Create Workflow Template**.
   - Add multiple job templates as steps.

2. **Configure Approvals**:
   - Add approval nodes for critical steps.

3. **Launch and Test**:
   - Launch the workflow and monitor its execution.

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

