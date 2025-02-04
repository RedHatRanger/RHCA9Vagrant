# Topology Diagram: Ansible Automation Platform Lab

## **Network Topology Overview**

```plaintext
                     +-------------------+-------------------+
                     |                                       |
            +--------+--------------+           +------------+-------------+
            |    Database Node      |           |   Controller Node        |
            |    (db.example.com)   |           | (controller.example.com) |
            +--------+--------------+           +-----------+--------------+
                     |                                      |
         +-----------+-----------+                          |
         |                       |                          |
+--------+------------+   +--------+------------+      +------+-------------------+
|      PAH Node       |   |   Managed Node      |      |    GitLab Server         |
|    (hub.example.com)|   | (node1.example.com) |      | (gitlab.example.com) |
+---------------------+   +---------------------+      +--------------------------+
```

---

## **Explanation**

- **Workstation (Ansible User)**:
  - The machine used to manage the environment via Ansible playbooks.
- **Controller Node**:
  - The central component for managing workflows, job templates, and inventories.
- **Database Node**:
  - A dedicated PostgreSQL server used by the Controller and PAH.
- **PAH Node (Private Automation Hub)**:
  - A repository for Ansible Content Collections and Execution Environments.
- **Worker Node**:
  - Executes playbooks dispatched by the Controller.
- **GitLab Server**:
  - Runs as a Podman container on the Controller node, providing version control and CI/CD functionality.

---



