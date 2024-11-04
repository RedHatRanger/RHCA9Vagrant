
# Guided Exercise: Troubleshooting with Red Hat Resources

## Objective

- You should be able to generate and inspect an sos report to aid Red Hat Support in resolving an issue.

### Initial Setup:

1. Log in to **node1** as the **bob** user and switch to the root user:

   \`\`\`bash
   [bob@workstation ~]$ ssh bob@node1
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   \`\`\`

### Problem Scenario

- While troubleshooting an issue on the **node1** machine, you contacted Red Hat Support for assistance. Support requested that you generate an **sos report** for only the **xfs** plugin.
- For the purpose of this exercise, your case number is **123456**.

### Step 1: Verify and Install \`sos\`

1.1. Verify that the **sos** package is installed:

   \`\`\`bash
   [root@node1 ~]# rpm -q sos
   sos-4.0-11.el8.noarch
   \`\`\`

- If the package is not installed, you will see **package sos is not installed**.

1.2. If necessary, install the **sos** package:

   \`\`\`bash
   [root@node1 ~]# yum install sos
   \`\`\`

### Step 2: View Available Options for \`sos report\`

2.1. View the available options for the **sos report** command:

   \`\`\`bash
   [root@node1 ~]# sos report --help | less
   \`\`\`

2.2. View the available plug-ins and plug-in options for the **sos report** command:

   \`\`\`bash
   [root@node1 ~]# sos report -l | less
   \`\`\`

### Step 3: Generate an \`sos report\` for the XFS Plugin

3.1. Run the **sos report** tool with the **-o xfs** option, and use the interactive prompts to enter all required information:

   \`\`\`bash
   [root@node1 ~]# sos report -o xfs
   \`\`\`

   - You will be prompted with the following messages:

     \`\`\`
     sosreport (version 4.0)
     This command will collect diagnostic and configuration information from
     this Red Hat Enterprise Linux system and installed applications.
     An archive containing the collected information will be generated in
     /var/tmp/sos.og2eg7gq and may be provided to a Red Hat support
     representative.
     Any information provided to Red Hat will be treated in accordance with
     the published support policies at:
      https://access.redhat.com/support/
     The generated archive may contain data considered sensitive and its
     content should be reviewed by the originating organization before being
     passed to any third party.
     No changes will be made to system configuration.
     Press ENTER to continue, or CTRL-C to quit. Enter
     \`\`\`

   - Enter the **case id** when prompted:

     \`\`\`
     Please enter the case id that you are generating this report for []: 123456
     \`\`\`

   - The report will be generated, and you will see output similar to:

     \`\`\`
     Your sosreport has been generated and saved in:
      /var/tmp/sosreport-node1-123456-2021-08-30-pgstdzb.tar.xz
      Size 3.76KiB
      Owner root
      md5 8d66fd763d397ce8de263bfdd042b7aa
     Please send this file to your support representative.
     \`\`\`

### Step 4: Copy the Report to Workstation and Extract It

4.1. Return to **workstation** as the **bob** user:

   \`\`\`bash
   [root@node1 ~]# exit
   [bob@node1 ~]$ exit
   [bob@workstation ~]$
   \`\`\`

4.2. Copy the generated file to the **bob** user's home directory on **workstation**:

   \`\`\`bash
   [bob@workstation ~]$ scp root@node1:/var/tmp/sosreport-node1-123456*.tar.xz .
   \`\`\`

4.3. Extract the generated file:

   \`\`\`bash
   [bob@workstation ~]$ tar xvf sosreport-node1-123456-2021-09-02-ytgpkbx.tar.xz
   \`\`\`

### Step 5: Inspect the Extracted sos Report

5.1. Inspect the extracted **sos report** by listing its contents:

   \`\`\`bash
   [bob@workstation ~]$ cd sosreport-node1-123456-2021-09-02-ytgpkbx
   [bob@workstation sosreport-node1-123456-2021-09-02-ytgpkbx]$ ls -l
   \`\`\`

5.2. Confirm the use of the **xfs** plug-in by inspecting the **xfs** configuration of **node1**:

   \`\`\`bash
   [bob@workstation sosreport-node1-123456-2021-09-02-ytgpkbx]$ cat sos_commands/xfs/xfs_info
   \`\`\`

### Step 6: Finish the Lab

- On the **workstation** machine, use the lab command to complete this exercise:
  
  \`\`\`bash
  [bob@workstation ~]$ lab finish strategy-redhatresources
  \`\`\`

  This ensures that resources from previous exercises do not impact upcoming exercises.
