# Lab Environment Requirements for Ansible Automation Platform (AAP)

## **VM Breakdown**

| **VM Name**        | **Role**                          | **Description**                                                                                  |
|---------------------|------------------------------------|--------------------------------------------------------------------------------------------------|
| **Controller**      | Automation Controller             | Central management for workflows, projects, inventories, and job templates.                      |
| **PAH**             | Private Automation Hub            | Internal repository for Ansible content collections and Execution Environments.                  |
| **EDA Controller**  | Event-Driven Controller           | Listens for events and triggers automation based on rulebooks.                                   |
| **Database**        | PostgreSQL Server                 | Shared database for Automation Controller, PAH, and Event-Driven Controller.                     |
| **Worker Node**     | Execution Node                   | Executes Ansible playbooks dispatched by the Automation Controller.                              |
| **GitLab (Container)** | GitLab Server                   | GitLab server running in a Podman container on the Controller node.                              |

---

## **VM Resource Allocation**

| **VM Name**        | **vCPUs** | **RAM** | **Disk Space** |
|---------------------|-----------|---------|----------------|
| **Controller**      | 2         | 4 GB    | 40 GB          |
| **PAH**             | 1         | 2 GB    | 20 GB          |
| **EDA Controller**  | 1         | 2 GB    | 20 GB          |
| **Database**        | 1         | 2 GB    | 20 GB          |
| **Worker Node**     | 1         | 2 GB    | 20 GB          |

---

## **Total Resource Requirements**
To host the lab environment with 5 VMs and a GitLab container, ensure your system meets the following:

### **CPU**
- **Total vCPUs Required**: 6.
- **Recommended Host CPU**:
  - At least 4 physical cores with hyper-threading enabled (e.g., Intel Core i5 or AMD Ryzen 5).

### **RAM**
- **Total RAM Required**: 12 GB.
- **Recommended Host RAM**:
  - 16 GB (to allocate 12 GB for VMs, leaving 4+ GB for the host OS).
  - Preferably 24 GB for additional overhead.

### **Disk Space**
- **Total Disk Space Required**: 120 GB.
- **Recommended Host Disk**:
  - At least 150 GB SSD for better performance.
  - SSD is highly recommended for I/O-intensive operations like database queries.

## **Network Configuration**
- Use static IP addresses for each VM for consistent connectivity.
- Example IP assignments:
  - **Controller**: `192.168.56.12`
  - **PAH**: `192.168.56.11`
  - **EDA Controller**: `192.168.56.13`
  - **Database**: `192.168.56.10`
  - **Worker Node**: `192.168.56.14`

- Ensure `/etc/hosts` is updated for hostname resolution across all VMs:
```plaintext
192.168.56.10 db.local
192.168.56.11 pah.local
192.168.56.12 controller.local
192.168.56.13 eda.local
192.168.56.14 worker.local
```

---

## **GitLab Installation on Controller**
To set up GitLab on the Controller node using Podman:

1. **Install Podman on the Controller Node**:
   ```bash
   sudo dnf install -y podman
   ```

2. **Run the GitLab Container**:
   ```bash
   sudo podman run --detach \
       --hostname gitlab.local \
       --publish 8929:8929 --publish 2224:22 --publish 9443:443 \
       --name gitlab \
       --restart always \
       --volume /srv/gitlab/config:/etc/gitlab \
       --volume /srv/gitlab/logs:/var/log/gitlab \
       --volume /srv/gitlab/data:/var/opt/gitlab \
       gitlab/gitlab-ce:latest
   ```

3. **Access GitLab**:
   - **Web Interface**: `https://controller.local:9443`
   - **SSH Access**: `ssh gitlab@controller.local -p 2224`

---

## **Summary**
This setup includes 5 VMs and a GitLab container for a full-featured lab environment to study and test the Ansible Automation Platform:
- Matches real-world deployment scenarios.
- Includes GitLab on the Controller node for version control and CI/CD.
- Provides isolation for different AAP components.
- Allows flexibility in combining roles if resources are limited.

