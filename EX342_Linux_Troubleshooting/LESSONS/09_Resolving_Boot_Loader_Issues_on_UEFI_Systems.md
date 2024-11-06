# **Lesson 9: Resolving Boot Loader Issues on UEFI Systems**

## **Objectives**

- Repair boot issues on UEFI systems.

## **Understanding the UEFI Firmware Interface**

Introduced in 2005, the Unified Extensible Firmware Interface (UEFI) replaced the original Extensible Firmware Interface (EFI) and the legacy BIOS. UEFI overcomes many of the limitations of BIOS, such as the use of 16-bit processor mode and the limit of 1 MiB of addressable space. It also supports booting from larger disks (over 2 TiB) if the disk and its partitions use a GUID Partition Table (GPT) format.

UEFI systems and BIOS systems have different methods of booting. A BIOS system must search for bootable operating systems on devices, while UEFI systems register bootable operating systems within the UEFI firmware itself. This allows users of UEFI systems to more easily select an operating system in a multiboot environment.

## **Using GRUB2 to Boot UEFI Systems**

To boot a UEFI system with GRUB2, an EFI System Partition (ESP) must be present on the installed disk. It is recommended to use GPT partitioning for UEFI systems, although MBR partitioning is also possible. The ESP partition must be formatted with a FAT file system and large enough to store the boot loader and all bootable kernels. The recommended size is 512 MiB, though smaller sizes might suffice. The ESP partition is typically mounted at `/boot/efi` and created by the `anaconda` installer during setup.

## **The UEFI Boot Chain**

A Secure Boot-capable system uses digital signatures on boot loaders, kernels, and other boot objects. The system firmware verifies these signatures to authenticate that only trusted code is loaded. This process uses a component called the shim UEFI boot loader.

- **shim.efi**: This application is signed with a key trusted by UEFI firmware. When executed, shim.efi attempts to load `grubx64.efi` through standard UEFI calls. If the signature does not verify, shim will attempt to load using other keys.
- **shim and MokManager**: Alternative keys are either compiled into shim or read from a Machine Owner Key (MOK) stored in UEFI NVRAM. When trying to start a signed application without registered keys, `MokManager.efi` starts so that the user can register a personal key.

If `shim.efi` is not registered, booting from the ESP partition will execute the `/boot/efi/EFI/BOOT/BOOTX64.efi` file, which will load `fallback.efi`, automatically register `shim.efi`, and boot the system.

## **Key Differences in GRUB2 Between BIOS and UEFI**

![{6BB0DF82-3E11-46D0-B15E-3B7489A247E7}](https://github.com/user-attachments/assets/4d17a116-b20b-46de-bede-e58f752f2c14)


### **Commands and Directory Changes**

- On UEFI systems, the GRUB2 kernel loading commands (`linux16` and `initrd16`) change to `linuxefi` and `initrdefi` respectively, since kernels load differently on UEFI systems compared to BIOS systems.
- Configuration files are located at `/boot/efi/EFI/redhat` rather than `/boot/grub2` as used on BIOS-based systems. This location is necessary for UEFI firmware access.

### **Warning About grub2-install**

Do **not** use the `grub2-install` command directly to install the UEFI boot loader. Red Hat Enterprise Linux (RHEL) 9 provides a prebuilt `/boot/efi/EFI/redhat/grubx64.efi` file, which contains the required signatures for Secure Boot. Using `grub2-install` on a UEFI system generates a generic `grubx64.efi` file without the required signatures, which will cause the system to fail to boot if Secure Boot is enabled.

## **Managing UEFI Boot Targets**

RHEL 9 provides the `efibootmgr` utility to manage available boot targets and logical boot order. This utility is part of the `efibootmgr` package, which is installed by default on UEFI systems.

- **Viewing Boot Targets**: Use `efibootmgr` to view the current boot targets:
  ```bash
  [root@host ~]# efibootmgr
  BootCurrent: 0003
  Timeout: 0 seconds
  BootOrder: 0003,0002,0000
  Boot0000* UiApp
  Boot0002* UEFI Misc Device
  Boot0003* Red Hat Enterprise Linux
  ```
  The boot entries are listed, with the order of boot preference from left to right. The `*` indicates active and available boot entries.

- **Adding/Removing Entries**: You can use the `-B` option to delete boot entries or the `-c` option to create new ones. For example, the following command deletes entry `0x0004`:
  ```bash
  [root@host ~]# efibootmgr -b 4 -B
  ```

## **Using grubby for UEFI Firmware**

The `grubby` tool can be used to manipulate kernel options and manage boot loaders on UEFI systems. You need root privileges to use `grubby`.

- Obtain the current default kernel:
  ```bash
  [root@host ~]# grubby --default-kernel
  /boot/vmlinuz-4.18.0-305.el9.x86_64
  ```

- Change the default kernel:
  ```bash
  [root@host ~]# grubby --set-default /boot/vmlinuz-4.18.0-305.el9.x86_64
  ```

## **Reinstalling GRUB2 on UEFI-Based Systems**

To recover damaged or missing `/boot/efi` files, reinstall the `grub2-efi` and `shim` packages:

```bash
[root@host ~]# yum reinstall grub2-efi shim
```

If the `/boot/efi/EFI/redhat/grub.cfg` file is missing, regenerate it with:

```bash
[root@host ~]# grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
Generating grub configuration file ...
done
```

If the RHEL 9 boot entry is missing from the UEFI boot menu, it will be automatically restored during the next system boot.

