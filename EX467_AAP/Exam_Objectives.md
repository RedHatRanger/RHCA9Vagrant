# EX467 Exam Objectives
[Link](https://www.youtube.com/watch?v=N1GhMAKOMiE) to YouTube Video by Luca Berton \
[Link](https://www.redhat.com/en/interactive-labs/ansible) to the Red Hat Official AAP Labs \
[Link](https://www.amazon.com/Demystifying-Ansible-Automation-Platform-definitive-ebook/dp/B0B579GB1N/ref=tmm_kin_swatch_0?_encoding=UTF8&sr=) to the best Amazon Book on AAP \
[Link](https://access.redhat.com/downloads/content/480/ver=2.4/rhel---9/2.4/x86_64/product-software) to download the Ansible Automation Platform Setup Bundle \
[Link](https://access.redhat.com/management/api) to generate an offline API Token

## 1. [Install Ansible Automation Platform](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/red_hat_ansible_automation_platform_installation_guide/disconnected-installation#disconnected-installation) and [HERE](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/red_hat_ansible_automation_platform_installation_guide/assembly-platform-install-scenario#ref-single-controller-hub-ext-database-inventory)
- [Private Automation Hub](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/red_hat_ansible_automation_platform_installation_guide/index#proc-verify-hub-installation_platform-install-scenario)
- [Automation Controller](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/red_hat_ansible_automation_platform_installation_guide/index#proc-verify-controller-installation_platform-install-scenario)
- [Configure Automation Controller Post-Installation](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/automation_controller_administration_guide/index)

## 2. Configure Private Automation Hub
- [Upload content collections to Private Automation Hub](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/managing_content_in_automation_hub/managing-collections-hub#proc-uploading-collections)
- [Upload execution environments to Private Automation Hub](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/creating_and_consuming_execution_environments/assembly-publishing-exec-env)

## 3. Manage access to Private Automation Hub
- [Create groups and assign different permissions to groups](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/getting_started_with_automation_hub/assembly-user-access#proc-create-group)
- [Create users and assign them to groups](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/getting_started_with_automation_hub/assembly-user-access#proc-create-users)
- [Configure User Types](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/getting_started_with_automation_hub/assembly-user-access#proc-create-super-users)

### Manage access for automation controller
- Create automation controller [users](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/assembly-controller-users#proc-controller-creating-a-user) and [teams](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/assembly-controller-teams#proc-controller-creating-a-team)
- [Associate users with teams associations](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/assembly-controller-teams#adding_or_removing_a_user_to_a_team)
- [Configure Organization Roles](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/assembly-controller-organizations)
- [Configure User Types](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/assembly-controller-users#proc-controller-creating-a-user)

## 4. Manage Inventories and Credentials
- [Create Host Groups](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/getting_started_with_automation_controller/index#controller-add-groups-and-hosts)
- [Assign Systems to Host Groups](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/getting_started_with_automation_controller/index#controller-add-groups-and-hosts)
- [Configure Access to Inventories](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-inventories#proc-controller-adding-inv-permissions)
- [Set Inventory Variables](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-inventories#ref-controller-group-name-vars-filtering) and [Here](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/red_hat_ansible_automation_platform_installation_guide/appendix-inventory-files-vars)
- [Create and Configure Machine Credentials](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-credentials#controller-getting-started-create-credential)

## 5. Manage Automation Controller Projects
- [Create Projects](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-projects#proc-controller-adding-a-project)
- [Create Job Templates](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-job-templates#controller-create-job-template)
- [Control User Access to Job Templates](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/automation_controller_user_guide/index#controller-adding-permissions_templates)
- [Launch Jobs](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html-single/automation_controller_user_guide/index#controller-launch-job-template)
- [Create a Job Template Survey](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-job-templates#controller-surveys-in-job-templates)
- [Use Variables from a Survey in a Jinja Template](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-job-templates#controller-extra-variables)
- [Schedule Jobs](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-job-templates#controller-scheduling-job-templates) and [Manage Notifications](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-notifications#controller-notifications)

## 6. Manage automation controller workflows
- [Create workflow templates](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-workflow-job-templates#controller-create-workflow-template)
- [Launch workflow jobs](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-workflow-job-templates#controller-launch-workflow-template)
- [Configure workflow jobs for automatic approval](

## 7. Manage advanced inventories
- [Import external static inventories](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-tips-and-tricks#ref-controller-import-inventory-files)
- [Filter hosts with Smart Inventories](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-inventories#ref-controller-smart-host-filter)

## 8. Work with the automation controller API
- [Understand how to use an API script to obtain required parameters for launching jobs](
- [Write an API scriptlet to launch a job](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_api_overview/controller-api-auth-methods#controller-api-oauth2-token)

## 9. Install and configure automation mesh
- [Install Automation Mesh](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/red_hat_ansible_automation_platform_automation_mesh_guide_for_vm-based_installations/setting-up#install-mesh_setting-up)
- [Create and Manage Instance Groups](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_user_guide/controller-instance-groups#controller-create-instance-group)
- [Assign Default Instance Groups to Controller Inventories or Templates](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-instance-and-container-groups#controller-control-job-run)
- [Run Controller Job Templates in a Specific Instance Group](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-instance-and-container-groups#controller-control-job-run)

## 10. Back Up Ansible Automation Platform
- [Back Up an Instance of Automation Controller](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-backup-and-restore#controller-backup-and-restore)
- [Back Up an Instance of Ansible Private Hub](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4/html/automation_controller_administration_guide/controller-backup-and-restore#controller-backup-and-restore)

## Exam Considerations
- **As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.**
- LINK TO THE OFFICIAL EXAM OBJECTIVES [HERE:](https://www.redhat.com/en/services/training/ex467-red-hat-certified-specialist-managing-automation-ansible-automation-platform-exam?section=objectives)


