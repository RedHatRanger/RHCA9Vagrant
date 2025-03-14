# **LESSON 8: Resolving Boot Loader Issues on BIOS Systems**

## **Objectives**

- Repair boot issues on BIOS systems.

## **Describing the BIOS Firmware Interface**

The Basic Input/Output System (BIOS) is a firmware interface that manages the initial stages of the boot process and provides a low-level interface to hardware devices.

The BIOS checks the system and locates a bootable device containing a Master Boot Record (MBR) to initiate the boot process. In Red Hat Enterprise Linux 9, the MBR boot loader uses the GRand Unified Bootloader version 2 (GRUB2). The BIOS loads the boot loader from the MBR into memory, and the boot loader subsequently loads and transfers control to the GRUB2 boot menu.

## **Storing the Master Boot Record (MBR)**

GRUB2 offers two options for booting from a disk on a BIOS system: storing the initial part of the GRUB2 boot loader in the MBR of a disk or placing it in the first sector of a bootable partition.

### **Limitation of Using MBR**

Using the MBR has a size limitation. A typical MBR contains only 512 bytes, which includes the disk's partition table, leaving just 446 bytes for the boot loader. To overcome this limitation, GRUB2 can utilize the MBR gap, also known as the boot track or embedding area, where additional boot modules and files are stored. The MBR gap is the space between the MBR block and the first disk partition.

To work reliably for modern boot loaders, the MBR gap must be at least 31 KiB (63 sectors on a 512-byte sector disk). Disks prepared using anaconda, the RHEL installation utility, typically start the first partition at sector 2048, creating a 1 MiB MBR gap for storing GRUB2 elements.

## **Booting RHEL 9 BIOS Systems Using GRUB2**

The boot loader is the first program executed when the system starts. It locates, loads, and transfers control to an operating system. GRUB2 can boot any compatible operating system or transfer control to other boot loaders for unsupported systems.

For systems with an existing operating system, the anaconda installer attempts to automatically detect and configure the boot loader to start the other operating system. If the boot loader is not detected, you can manually configure additional operating systems after installation. Partition requirements vary depending on whether the system uses BIOS or UEFI. Modern systems may also require GPT partitioning, which supports larger disks and offers greater flexibility compared to MBR partitioning while still supporting MBR boot loaders. If a user selects automatic partitioning in anaconda, the installer creates the necessary partition type with sufficient MBR gap.

GRUB2 reads its configuration from the `/boot/grub2/grub.cfg` file on traditional BIOS-based machines and from the `/boot/efi/EFI/redhat/grub.cfg` file on UEFI-based machines.

The GRUB2 utilities store files on a BIOS system in several locations:

- The `/boot/` directory stores the kernel and initial ramdisk.
- The `/boot/grub2/` directory contains configuration files and extension modules.
- The `/boot/grub2/grub.cfg` file is the configuration file, with a symbolic link to `/etc/grub2.cfg`.
- The `/etc/grub.d/` directory contains a helper script to generate the `/boot/grub2/grub.cfg` file.
- The `/etc/default/grub` file contains variables used by the configuration helper script.
- The `/boot/grub2/grubenv` file is exactly 1 KiB and stores variables such as the default or saved boot entry.

## **Describing the GRUB2 Configuration File**

Typically, a system administrator does not manually configure the GRUB2 boot loader. During installation, a new kernel is added to the GRUB2 configuration file automatically, and the corresponding boot loader entry is removed when a kernel is removed. To temporarily change the GRUB2 menu at boot time, parameters can be passed to the kernel during startup using the GRUB2 menu.

Some parameter options are available in the `/etc/default/grub` file:

- **GRUB_TIMEOUT**: Specifies the number of seconds the GRUB2 menu is displayed before the default entry is selected automatically.
- **GRUB_DEFAULT**: Specifies the default entry that starts if no other entry is selected. GRUB2 entries are counted starting from 0.
- **GRUB_CMDLINE_LINUX**: Contains a list of extra kernel command-line options to be added to every Linux kernel.

After updating the `/etc/default/grub` file, run the `grub2-mkconfig -o /boot/grub2/grub.cfg` command to apply the changes. This command uses the configuration helper scripts and settings to generate a new configuration file. The `-o` option redirects the command's standard output to the specified file.

## **The Red Hat Enterprise Linux 9 Boot Sequence**

Booting Red Hat Enterprise Linux 9 on a physical x86_64 system is a sequential process. For x86_64 virtual machines, the hypervisor handles some of the hardware-specific steps.

1. When the machine powers on, the BIOS or UEFI firmware runs a Power On Self Test (POST) and initializes critical hardware.
2. The firmware searches for a bootable device, configured in the firmware, by locating an MBR on each disk.
3. The firmware finds a boot loader on the bootable device and transfers control to it. In an RHEL 9 system, the boot loader is GRUB2, installed using the `grub2-install` command.
4. GRUB2 loads its configuration from the `/boot/grub2/grub.cfg` file and displays a menu with options to select a kernel to boot.
5. The boot loader loads the kernel and the initramfs file system from the device into memory after selecting a kernel or when the timeout expires. The initramfs file system contains a minimal set of drivers and kernel modules necessary for bootstrapping the system.
6. The boot loader passes control to the kernel, specifying kernel command line options and the memory location of the initramfs file system.
7. The kernel initializes critical hardware using drivers from the initramfs file system and executes `/sbin/init` from the initramfs file system as PID 1. In RHEL 9, `/sbin/init` is a link to systemd.
8. The `systemd` daemon executes all units for the `initrd.target` and mounts the configured root file system on the `/sysroot` directory.
9. The kernel switches (pivots) the root file system from the initramfs file system to the root file system in `/sysroot`. The `systemd` daemon re-executes itself from the root file system.
10. The `systemd` daemon activates the default target, which is either specified by the kernel command line or linked to `/etc/systemd/system/default.target`. `Systemd` starts or stops units to comply with the target's configuration and resolves dependencies between units automatically.
11. `Systemd` targets that act as run levels (with an `AllowIsolate=true` parameter) activate their units and display a text-based or graphical login prompt, indicating that the system is booted and ready for user login.

## **Updating Boot Loader Configuration Files**

The Boot Loader Specification (BLS) manages boot loader configuration by defining a scheme and file format without directly modifying boot loader configuration files.

With BLS, each boot entry references a separate configuration file in the drop-in directory. This architecture allows the configuration to be extended without needing to edit or regenerate the main boot loader configuration files.

BLS also uses this concept to manage boot menu entries. Boot loader menu options can be managed by adding, removing, or editing individual boot entry files in the drop-in directory. BLS greatly simplifies the kernel installation process and is consistent across different architectures.

### **Generate BLS Configuration Files**

1. Add the following entry to the `/etc/default/grub` file:

   ```
   GRUB_ENABLE_BLSCFG=true
   ```

2. For a legacy BIOS system, generate the `/boot/grub2/grub.cfg` file:

   ```bash
   [root@host ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
   Generating grub configuration file ...
   done
   ```

3. Generate the `/boot/loader/entries`:

   ```bash
   [root@host ~]# kernel-install add $(uname -r) /lib/modules/$(uname -r)/vmlinuz
   ```

## **Making Persistent Changes to the GRUB2 Boot Menu**

The `grubby` utility manipulates boot loader-specific configuration files and acts as a wrapper for BLS operations. Use `grubby` to change the default boot entry or to add or remove arguments in a GRUB2 menu entry.

If `grubby` is used without specifying a GRUB2 configuration file, it defaults to locating `grub2.cfg`.

- Obtain the file name of the default kernel using `grubby`:

  ```bash
  [root@host ~]# grubby --default-kernel
  /boot/vmlinuz-4.18.0-305.el8.x86_64
  ```

- Find the index number of the default kernel:

  ```bash
  [root@host ~]# grubby --default-index
  0
  ```

- Use the `grubby` command to persistently change the default kernel:

  ```bash
  [root@host ~]# grubby --set-default /boot/vmlinuz-4.18.0-305.el8.x86_64
  ```

- List all kernel menu entries:

  ```bash
  [root@host ~]# grubby --info=ALL
  ```

- View the GRUB2 menu entry for a specific kernel:

  ```bash
  [root@host ~]# grubby --info /boot/vmlinuz-4.18.0-305.el8.x86_64
  ```

## **Reinstalling the GRUB2 Boot Loader on the MBR**

If the MBR or the MBR gap is damaged, reinstall GRUB2 into the MBR to overwrite it.

If the system is not running when this issue occurs, the repair must be performed from a Live CD or the rescue environment available from the anaconda installer. If a user with root permissions has access to a running system with this issue, the solution is to use the `grub2-install` command.

### **Steps to Reinstall GRUB2 in Rescue Environment**

1. Boot from an installation source, such as a RHEL DVD image, netboot CD, or a Preboot eXecution Environment (PXE) provided by a RHEL installation tree.
2. Append `inst.rescue` to the boot command line. Alternatively, select **Troubleshooting** and then **Rescue a Red Hat Enterprise Linux system** from the installation media menu, and press Enter.
3. From the menu, select option 1 to mount the file system under the `/mnt/sysroot` directory. To open a shell, press Enter.
4. Use `chroot` to switch the rescue mode environment to use the mounted root partition (`/mnt/sysroot`) of the system being repaired.

   ```
   sh-4.4# chroot /mnt/sysroot
   ```

5. Reinstall the GRUB2 boot loader on the previously-used block device.

   ```
   bash-4.4# /sbin/grub2-install /dev/vda
   ```

6. Reboot the system to verify that the GRUB2 boot loader is now operational.

## **Describing Early Kdump Support in RHEL 9**

Older RHEL versions were unable to capture kernel crashes occurring in the early stages of the boot process, because the kdump service's dependencies required it to start later in the process. Without a kernel dump, troubleshooting early boot process problems was challenging.

RHEL 9 introduces early kdump support to address this issue. Early kdump support can log kernel crashes during the kernel boot phase by storing a crash kernel vmlinuz and initramfs inside the initramfs of the normal booting kernel. Early kdump loads directly into reserved memory (`crashkernel`) during the early boot stage. Two dracut modules are provided in the `kexec-tools` package to load the crash kernel and initramfs as early as possible during the boot sequence, making it available to capture a crash dump of the normal booting kernel.
