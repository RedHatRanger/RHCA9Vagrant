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

