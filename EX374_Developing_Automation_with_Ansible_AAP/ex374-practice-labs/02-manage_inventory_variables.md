# Lab 02 (AAP Edition): Manage Inventory Variables in Ansible Automation Platform

Below is a concise lab guide for managing Ansible inventory files from a Git repo, then using that inventory in Ansible Automation Platform (AAP). This extends the local approach (multiple group/host variable files) into the AAP environment.

---

## 1. Overview

Here are the [EX374 Exam Objectives](https://www.redhat.com/en/services/training/red-hat-certified-specialist-developing-automation-ansible-automation-platform-exam?section=objectives):
1. Store your inventory files (with `group_vars` and `host_vars`) in a Git repo.
2. Configure a Project in AAP that pulls this repo.
3. Define a new Inventory in AAP linked to that Project.
4. Show how Ansible Automation Platform reads host/group variables.
5. Test with a sample Job Template.

---

## 2. Prerequisites

1. **Ansible Automation Platform** up and running with admin or org-level access.
2. A **Git repository** containing:
   - An `inventory` file.
   - Folders: `group_vars/` and `host_vars/`.
3. A **Credential** in AAP (Git type or SCM type) if your repo is private.

Example Repo Layout:

```
lab02_inventory/
├── inventory
├── group_vars
│   ├── web_servers.yml
│   └── db_servers.yml
├── host_vars
│   ├── server1.yml
│   ├── server2.yml
│   └── server3.yml
└── test_inventory.yml
```

---

## 3. Push Inventory to Git

If you haven’t already:
1. Create a **Git repo** on GitHub, GitLab, or local.
2. Commit/push the `lab02_inventory/` contents.
3. Note the **Clone URL** (SSH or HTTPS) for use in AAP.

---

## 4. Create a Project in AAP

1. **Log in** to your AAP (Automation Controller).
2. Go to **Projects** → **Add**.
3. Fill in:
   - **Name**: "Lab02 Inventory Project"
   - **Organization**: (select your org)
   - **Source Control Type**: Git
   - **SCM URL**: e.g. `git@github.com:youruser/lab02_inventory.git`
   - **SCM Credential**: your SSH or HTTPS credential (if needed)
4. Click **Save**.
5. (Optional) **Sync** the project to pull the files.

---

## 5. Create an Inventory in AAP

1. Go to **Inventories** → **Add**.
2. **Name**: "Lab02 Inventory"
3. **Organization**: (your org)
4. **Inventory Type**: Inventory
5. Click **Save**.
6. In the new inventory’s **Sources** tab, click **Add**:
   - **Source**: Sourced from a Project
   - **Project**: "Lab02 Inventory Project" (the one created above)
   - **Inventory File**: `lab02_inventory/inventory` (or the relative path if needed)
   - Set **Update Options** as needed.
   - Click **Save**.
7. **Sync** the inventory source. AAP will read your `inventory` file along with any group/host variables from `group_vars/` and `host_vars/`.

---

## 6. Test in a Job Template

1. **Create a Playbook** named `test_inventory.yml`, referencing the variables (same as the prior local example):

   ```yaml
   ---
   - name: Test inventory variables
     hosts: all
     gather_facts: false

     tasks:
       - name: Show host vars
         debug:
           msg: "Host: {{ inventory_hostname }} | site_name={{ site_name|default('N/A') }}"
   ```

2. **In AAP**, go to **Templates** → **Add** → **Job Template**:
   - **Name**: "Test Lab02 Inventory"
   - **Inventory**: "Lab02 Inventory"
   - **Project**: "Lab02 Inventory Project"
   - **Playbook**: `lab02_inventory/test_inventory.yml` (path to your test playbook)
   - Click **Save**.
3. Click **Launch** and check the output. You should see debug messages with your variables.

---

## 7. Conclusion

You have now:

1. Put a multi-file Ansible inventory (with group/host vars) in **Git**.
2. Created a **Project** in AAP that syncs that repo.
3. Set up an **Inventory** in AAP that imports data from the local `inventory` file.
4. Successfully run a **Job Template** reading all your group_vars and host_vars.

This demonstrates how local inventory variable concepts translate into the Automation Controller’s environment. Continue practicing by adding extra hosts, groups, or more complex variable structures.
