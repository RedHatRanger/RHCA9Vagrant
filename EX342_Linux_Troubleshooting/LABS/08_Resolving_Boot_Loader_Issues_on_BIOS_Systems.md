# **Lab 8: Resolving Boot Loader Issues on BIOS Systems**

## **Objective**

- Fix a GRUB2 boot issue on a BIOS-based machine.

## **Outcomes**

- Restore the GRUB2 boot menu on a BIOS-based system using the `grubby` command.

## **Before You Begin**

### Manual Lab Setup:

1. **Simulate the Boot Issue**:
   - Log in to the **node1** machine and ensure you have root privileges.
   - Create a faulty boot entry by running the following command:

   ```bash
   [root@node1 ~]# grubby --copy-default --make-default --args="server"
   ```

2. **Boot with Incorrect Kernel**:
   - Restart the machine and try booting with the incorrect kernel entry to see the error messages.

3. **Manually Fix the Boot Issue**:
   - Follow the steps outlined in the lab instructions below.

## **Instructions**

The **node1** machine was updated to boot with an incorrect kernel. The incorrect boot entry has a label of "server". Change the default GRUB2 boot menu entry to use the kernel entry that does not have the "server" label.

1. **Open the node1 machine console**
   
   View the error message, which shows that the kernel is absent:

   ```
   error: ../../grub-core/loader/i386/pc/linux.c:170: invalid magic number.
   error: ../../grub-core/loader/i386/pc/linux.c:421: you need to load the kernel first.
   Press any key to continue...
   ```

2. **Boot the node1 machine with the correct kernel**
   
   Select the kernel entry that does not have the "server" label. You should see the login screen of the node1 machine:

   ```
   ...output omitted...
   node1 login:
   ```

3. **Log in to node1**
   
   Log in as the bob user, then switch to root and run the `grubby` command to query the boot entries:

   ```bash
   node1 login: bob
   Password: bob
   [bob@node1 ~]$ sudo -i
   [sudo] password for bob: redhat
   [root@node1 ~]# grubby --info=ALL
   ```

   Output:

   ```
   index=0
   kernel="/boot/vmlinuz-server"
   args="ro no_timer_check net.ifnames=0 crashkernel=auto"
   root="/dev/vda3"
   initrd="/boot/initramfs-server.img"
   title="Red Hat Enterprise Linux (server) 9.0 (Ootpa)"
   id="81a31fe4555b4603a2e19b6ec313f8b9-server.0~custom"

   index=1
   kernel="/boot/vmlinuz-server"
   args="ro no_timer_check net.ifnames=0 crashkernel=auto"
   root="/dev/vda3"
   initrd="/boot/initramfs-server.img"
   title="Red Hat Enterprise Linux (server) 9.0 (Ootpa)"
   id="81a31fe4555b4603a2e19b6ec313f8b9-server"

   index=2
   kernel="/boot/vmlinuz-4.18.0-305.el9.x86_64"
   args="ro no_timer_check net.ifnames=0 crashkernel=auto $tuned_params"
   root="/dev/vda3"
   initrd="/boot/initramfs-4.18.0-305.el9.x86_64.img $tuned_initrd"
   title="Red Hat Enterprise Linux (4.18.0-305.el9.x86_64) 9.0 (Ootpa)"
   id="ffffffffffffffffffffffffffffffff-4.18.0-305.el9.x86_64"
   ```

4. **Query the default GRUB2 boot entry**
   
   Use the `grubby` command to check the current default kernel:

   ```bash
   [root@node1 ~]# grubby --default-kernel
   /boot/vmlinuz-server
   ```

5. **Change the default GRUB2 menu entry**
   
   Set the default kernel to the one without the "server" label:

   ```bash
   [root@node1 ~]# grubby --set-default=/boot/vmlinuz-4.18.0-305.el9.x86_64
   The default is /boot/loader/entries/ffffffffffffffffffffffffffffffff-4.18.0-305.el9.x86_64.conf with index 2 and kernel /boot/vmlinuz-4.18.0-305.el9.x86_64
   ```

6. **Verify the default kernel**
   
   Confirm that the default kernel is set correctly:

   ```bash
   [root@node1 ~]# grubby --default-kernel
   /boot/vmlinuz-4.18.0-305.el9.x86_64
   ```

7. **Reboot the system**
   
   Reboot the node1 machine and verify that the default GRUB2 menu entry boots correctly and displays the login prompt:

   ```bash
   [root@node1 ~]# reboot
   ...output omitted...
   node1 login:
   ```

   You should now see the correct kernel being used for booting.

   * Done!!

