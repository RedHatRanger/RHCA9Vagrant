# Topology Diagram: Ansible Automation Platform Lab

## **Network Topology Overview**

```plaintext
                                +-------------------+
                                |   Workstation     |
                                |  (Ansible User)   |
                                +-------------------+
                                         |
                              +----------+----------+
                              |                     |
                     +-----------------+   +-------------------+
                     | Platform Gateway|   | EDA Controller    |
                     |  (gateway.local)|   |  (eda.local)      |
                     +--------+--------+   +--------+----------+
                              |                     |
         +--------------------+---------------------+------------------+
         |                                                             |
+-------------------+                                   +-------------------+
|  Database Node    |                                   |    Controller     |
|  (db.local)       |                                   |  (controller.local)|
+-------------------+                                   +-------------------+
          |                                                       |
          |                                                       |
  +-------------------+                               +-------------------+
  |      PAH          |                               |   Worker Node     |
  | (pah.local)        |                               |  (worker.local)   |
  +-------------------+                               +-------------------+
