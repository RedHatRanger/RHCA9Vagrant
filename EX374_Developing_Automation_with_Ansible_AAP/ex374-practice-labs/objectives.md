# EX374 Exam Objectives with Associated Labs

This document maps each EX374 exam objective to the specific lab that covers it in this study guide.

## Objective: Manage Git repositories
**Covered in: [Lab 1: Git Repository Management](labs/01_git_repository_management.md)**
- Cloning repositories
- Creating branches
- Committing changes
- Pushing to remote repositories
- Managing merge conflicts

## Objective: Manage inventory files
**Covered in: [Lab 1: Git Repository Management](labs/01_git_repository_management.md)**
- Creating and organizing inventory files in Git
- Host variables
- Group variables
- Inventory structure

## Objective: Create playbooks with templates
**Covered in: [Lab 2: Ansible Playbook Development](labs/02_ansible_playbook_development.md)**
- Creating Jinja2 templates for configuration files
- Variable substitution
- Template conditionals
- Template loops

## Objective: Use variables in playbooks
**Covered in: [Lab 2: Ansible Playbook Development](labs/02_ansible_playbook_development.md)**
- Variable precedence
- Defining variables at different levels
- Using variables with templates for dynamic content

## Objective: Control privilege execution
**Covered in: [Lab 3: Task Execution Management](labs/03_task_execution_management.md)**
- Using become and become_user
- Managing sudo access
- Limiting privilege escalation

## Objective: Run selected tasks from playbooks
**Covered in: [Lab 3: Task Execution Management](labs/03_task_execution_management.md)**
- Using tags to selectively run tasks
- Using --start-at-task
- Limiting execution

## Objective: Use lookup plugins
**Covered in: [Lab 4: Data Transformation](labs/04_data_transformation.md)**
- File, env, password, and other lookup plugins
- Creating custom lookup plugins

## Objective: Implement advanced loops
**Covered in: [Lab 4: Data Transformation](labs/04_data_transformation.md)**
- Loop, with_items, with_dict, and other loop structures
- Loop control parameters
- Nested loops

## Objective: Manipulate network information variables
**Covered in: [Lab 4: Data Transformation](labs/04_data_transformation.md)**
- IP address manipulation
- Network calculations
- CIDR notation and subnet operations

## Objective: Run tasks on different hosts
**Covered in: [Lab 5: Task Delegation](labs/05_task_delegation.md)**
- Using delegate_to and local_action
- Run_once with delegation

## Objective: Control fact delegation
**Covered in: [Lab 5: Task Delegation](labs/05_task_delegation.md)**
- Delegate_facts
- Fact caching
- Managing facts across delegated tasks

## Objective: Create content collections
**Covered in: [Lab 6: Content Collection Management](labs/06_content_collections.md)**
- Collection structure and metadata
- Roles, plugins, and modules in collections

## Objective: Install content collections
**Covered in: [Lab 6: Content Collection Management](labs/06_content_collections.md)**
- Installing from Galaxy, automation hub, and local sources
- Collection dependencies and requirements files

## Objective: Use collections in playbooks
**Covered in: [Lab 6: Content Collection Management](labs/06_content_collections.md)**
- FQCN syntax
- Collection namespace
- Collection configuration and precedence

## Objective: Upload to automation hub
**Covered in: [Lab 6: Content Collection Management](labs/06_content_collections.md) and [Lab 7: Execution Environment Management](labs/07_execution_environments.md)**
- Publishing to private automation hub
- Versioning and metadata management

## Objective: Create execution environments
**Covered in: [Lab 7: Execution Environment Management](labs/07_execution_environments.md)**
- Building custom execution environments with ansible-builder
- Defining dependencies and custom Python packages

## Objective: Use in automation controller
**Covered in: [Lab 7: Execution Environment Management](labs/07_execution_environments.md) and [Lab 9: Automation Controller Management](labs/09_automation_controller_management.md)**
- Configuring execution environments in projects and job templates
- Default vs. custom execution environments

## Objective: Manage advanced inventories
**Covered in: [Lab 8: Inventory and Credential Management](labs/08_inventory_credential_management.md)**
- Inventory plugins and dynamic inventory sources
- Inventory variables and host patterns

## Objective: Create dynamic inventories
**Covered in: [Lab 8: Inventory and Credential Management](labs/08_inventory_credential_management.md)**
- Writing custom dynamic inventory scripts
- Configuring inventory plugins for dynamic sources

## Objective: Set up credentials
**Covered in: [Lab 8: Inventory and Credential Management](labs/08_inventory_credential_management.md)**
- Machine, network, SCM, and cloud credential types
- Credential management and security

## Objective: Run playbooks in controller
**Covered in: [Lab 9: Automation Controller Management](labs/09_automation_controller_management.md)**
- Job templates, surveys, and workflow templates
- Scheduling and notifications
- Using the Automation Controller web interface

## Objective: Pull content from Git/hub
**Covered in: [Lab 9: Automation Controller Management](labs/09_automation_controller_management.md)**
- Project synchronization and SCM integration
- Content from private repositories

## Note on Web Interface Coverage

For objectives that require working with the AAP GUI web interface, detailed instructions are included in:
- [Lab 8: Inventory and Credential Management](labs/08_inventory_credential_management.md)
- [Lab 9: Automation Controller Management](labs/09_automation_controller_management.md)

These labs provide step-by-step guidance with screenshots and explanations of the UI elements to help you navigate and use the Automation Controller and Private Automation Hub web interfaces effectively.
