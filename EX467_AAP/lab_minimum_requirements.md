# Lab Environment Requirements for Ansible Automation Platform (AAP)

## **VM Breakdown**

| **VM Name**        | **Role**                          | **Description**                                                                                  |
|---------------------|------------------------------------|--------------------------------------------------------------------------------------------------|
| **Controller**      | Automation Controller             | Central management for workflows, projects, inventories, and job templates.                      |
| **PAH**             | Private Automation Hub            | Internal repository for Ansible content collections and Execution Environments.                  |
| **EDA Controller**  | Event-Driven Controller           | Listens for events and triggers automation based on rulebooks.                                   |
| **Database**        | PostgreSQL Server                 | Shared database for Automation Controller, PAH, and Event-Driven Controller.                     |
| **Worker Node**     | Execution Node                   | Executes Ansible playbooks dispatched by the Automation Controller.                              |

---

## **VM Resource Allocation**

| **VM Name**        | **vCPUs** | **RAM** | **Disk Space** |
|---------------------|-----------|---------|----------------|
| **Controller**      | 2         | 4 GB    | 30 GB          |
| **PAH**             | 2         | 2 GB    | 20 GB          |
| **EDA Controller**  | 2         | 4 GB    | 30 GB          |
| **Database**        | 1         | 2 GB    | 20 GB          |
| **Worker Node**     | 1         | 2 GB    | 20 GB          |

---

## **Total Resource Requirements**
To host the lab environment with 5 VMs, ensure your system meets the following:

### **CPU**
- **Total vCPUs Required**: 8.
- **Recommended Host CPU**:
  - At least 6 physical cores with hyper-threading enabled (e.g., Intel Core i7 or AMD Ryzen 7).

### **RAM**
- **Total RAM Required**: 14 GB.
- **Recommended Host RAM**:
  - 16 GB (to allocate 14 GB for VMs, leaving 2+ GB for the host OS).
  - Preferably 24 GB for additional overhead.

### **Disk Space**
- **Total Disk Space Required**: 120 GB.
- **Recommended Host Disk**:
  - At least 150 GB SSD for better performance.
  - SSD is highly recommended for I/O-intensive operations like database queries.

---

## **Alternative Configurations**
If resource constraints are an issue, consider combining roles to reduce the number of VMs:

### **Option 1: Combine Database and Worker Node**
- Combine the PostgreSQL Server and Worker Node into a single VM.
- Adjust resources:
  - **vCPUs**: 2
  - **RAM**: 4 GB
  - **Disk Space**: 30 GB

### **Option 2: Combine PAH and EDA Controller**
- Run Private Automation Hub and Event-Driven Controller on a single VM.
- Adjust resources:
  - **vCPUs**: 2
  - **RAM**: 4 GB
  - **Disk Space**: 30 GB

---

## **Network Configuration**
- Use static IP addresses for each VM for consistent connectivity.
- Example IP assignments:
  - **Controller**: `192.168.56.12`
  - **PAH**: `192.168.56.11`
  - **EDA Controller**: `192.168.56.13`
  - **Database**: `192.168.56.10`
  - **Worker Node**: `192.168.56.14`

- Ensure `/etc/hosts` is updated for hostname resolution across all VMs.

---

## **Summary**
This setup includes 5 VMs for a full-featured lab environment to study and test the Ansible Automation Platform:
- Matches real-world deployment scenarios.
- Provides isolation for different AAP components.
- Allows flexibility in combining roles if resources are limited.

Let me know if additional details are needed or adjustments are required!
