# EXAM B: Test Your Knowledge Lab Exercise for Labs 8-11

## Objective

- Fix an issue where a system fails to boot past the boot loader screen.

### Outcomes

You should be able to repair issues where a system fails to boot past the boot loader.

### Initial Setup

> To simulate the boot failure on `node1`, run the following commands as root to introduce a configuration that prevents the system from booting correctly:

1. **Set a Corrupted Kernel as Default Using grubby**

   ```bash
   grubby --set-default=/boot/vmlinuz-bogus-kernel
   ```

   This command sets a non-existent kernel as the default, which will cause the system to fail to boot properly.

2. **Reboot the System**

   ```bash
   reboot
   ```

   After running this command, the system will fail to boot due to the incorrect kernel entry.

### Instructions

Someone decided to shorten the boot process of the `node1` system. During this process, they broke the ability of `node1` to boot to its default target. You are asked to make `node1` boot properly again.

1. **Open a Console to Your node1 Machine and Troubleshoot the Problem**
   1.1. What is displayed on the `node1` console? If a GRUB menu is displayed, attempt to boot the default (select) entry.

   ```
   error: ../../grub-core/loader/i386/pc/linux.c:170:invalid magic number.
   error: ../../grub-core/loader/i386/pc/linux.c:421:you need to load the kernel first.
   Press any key to continue...
   ```

   1.2. What can this message mean?

   - This error message typically indicates that no kernel is defined in the menu entry, or that the ramdisk is being loaded before the kernel.

2. **Temporarily Boot Your node1 System to a Working Configuration**
   2.1. What are the fix options? Verify whether a first entry boots, in this case, the rescue entry, or manually edit the configuration in memory to temporarily solve the issue.

   2.2. Use the GRUB menu to boot another working entry.

3. **Permanently Fix the Issue**
   3.1. Log in to the `node1` system and switch to the root user.

   ```
   [bob@workstation ~]$ ssh bob@node1
   ...output omitted...
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]#
   ```

   3.2. Use the list of installed kernels to re-create a working `/boot/grub2/grub.cfg` file.

   ```bash
   grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

   3.3. Use grubby to review the newly added entry and set it as the default boot entry.

   ```bash
   grubby --info=ALL
   grubby --set-default=/boot/vmlinuz-4.18.0-305.el8.x86_64
   ```

   3.4. Verify the solution by rebooting the node1 system.

   ```bash
   reboot
   ```

   * Done!!
