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

