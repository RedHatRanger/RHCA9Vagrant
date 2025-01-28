# VM Resource and Host System Requirements

## **VM Resource Summary**

| Node         | vCPUs | RAM   | Disk Space |
|--------------|-------|-------|------------|
| Database     | 1     | 2 GB  | 20 GB      |
| Controller   | 2     | 4 GB  | 30 GB      |
| PAH          | 2     | 2 GB  | 20 GB      |
| Worker       | 1     | 2 GB  | 20 GB      |
| **Total**    | **6** | **10 GB** | **90 GB** |

---

## **Recommended Host System Specifications**

### **CPU**
- **Minimum**: Quad-core processor (e.g., Intel Core i5 or AMD Ryzen 5 with at least 4 cores).
- **Recommended**: Hexa-core or better for smoother performance when running multiple VMs simultaneously.

### **RAM**
- **Minimum**: 16 GB (to allocate 10 GB to VMs and leave enough for the host OS).
- **Recommended**: 24–32 GB for multitasking or if additional services or software will run on the host.

### **Disk Space**
- **Minimum**: 150 GB free (to accommodate the VMs' disk requirements, system logs, and space for the host OS).
- **Recommended**: 250 GB SSD for better performance, especially when multiple VMs access disk simultaneously.

### **Storage Type**
- Use an SSD over HDD to significantly improve VM performance, especially for I/O-intensive operations like database queries or container management.

### **Network**
- An Ethernet or stable Wi-Fi connection is sufficient for local VM networking.






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
