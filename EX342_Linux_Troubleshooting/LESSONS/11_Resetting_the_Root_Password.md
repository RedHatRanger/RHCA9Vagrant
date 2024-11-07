# Lesson 11: Resetting the Root Password (RHEL 8)

## Objectives
- Reset the root password.

### Resetting the Root Password
When the root password for a system is lost or forgotten, an authorized administrator can reset it. There are several methods available, some of which require physical console access, while others can be done remotely, such as through an SSH connection.

If an active root session is still available on an unlocked terminal, use that session to change the password. Alternatively, any accessible account with sufficient `sudo` privileges can reset the root password.

Another method involves manually editing the `/etc/shadow` file by copying in a known password hash using an account that has `sudo` access to a text editor, or by editing the virtual machine's disk image using the `guestfish` command.

#### Rescue Mode (RHEL 8)
If other methods are not available or are unsuccessful, an administrator with physical system access can use the rescue mode provided by the **anaconda** installation program to boot from installation media or external media and reset the root password. This requires that either the system disks are not encrypted or that the encryption password is known.

If the firmware password is configured and known, you can boot from alternative devices on both BIOS and UEFI firmware. The anaconda rescue mode is accessed by booting from a Red Hat Enterprise Linux 8 boot media (such as a USB installation device) and selecting the **Troubleshooting** option in the boot menu.

#### Resetting the Root Password without External Media (RHEL 8)
When external boot media is not available or successful, administrators can interrupt the `systemd` startup sequence to gain access and reset the root password. This method requires physical console access, access through a remote management card, or a KVM switch, and knowledge of passwords for disk encryption and the boot loader (if configured).

This method involves interrupting the boot sequence as follows:

1. **Reboot the System**: Reboot and interrupt the boot loader timer by pressing any key (except **Enter**).

2. **Edit Boot Entry**:
    - Highlight the entry that would normally boot and press **e**.
    - Locate the line that starts with **linux** (it contains the kernel and kernel arguments).
    - Press **Ctrl+e** to move the cursor to the end of this line, and add **rd.break**.
    - Press **Ctrl+x** to boot with the modified parameters.

3. **Halt Initial Ramdisk Execution**: The system now boots into initial ramdisk (initramfs) and halts the startup sequence. If no prompt appears, press **Enter** to check if the prompt is obscured by kernel messages.

4. **Remount Root File System as Read/Write**:
    - The file system is currently mounted at the `/sysroot` directory with read-only permissions.
    - Run the command:
      ```bash
      switch_root:/# mount -o remount,rw /sysroot
      ```

5. **Change Root Directory to `/sysroot`**:
    ```bash
    switch_root:/# chroot /sysroot
    ```

6. **Reset the Root Password**:
    - Set the root password to a known value:
      ```bash
      sh-4.2# echo "root:newpassword" | chpasswd
      ```

7. **Force SELinux Relabel During Next Boot**:
    ```bash
    sh-4.2# touch /.autorelabel
    ```
    - **Important**: This relabel is mandatory, as SELinux needs to trust the system and relabel modified files to apply correct security contexts.

8. **Reboot the System**:
    - Exit from the `chroot` environment and from the `switch_root` prompt by typing **exit** twice.

9. **Verify Root Password Reset**:
    - Log in as the `root` user or log in as a non-privileged user and switch to root to verify that the root password has been successfully reset.

