# Lab 12: Identifying Hardware Issues

## Objectives
- Identify system hardware and view hardware error reports.

### Outcomes
You should be able to identify your system's hardware and view hardware errors with `rasdaemon`.

### Before You Begin
As the student user on the workstation machine, use the lab command to prepare your system for this exercise.

```bash
[student@workstation ~]$ lab start hardware-identify
```

This command verifies that your systems are reachable and generates the necessary files for this exercise.

### Instructions
You collect hardware information from `servera`, and list hardware errors that `rasdaemon` reports. Use the `/root/systeminfo.txt` file on `servera` to record hardware information.

1. **Login to `servera` and switch to the root user**.

   ```bash
   [student@workstation ~]$ ssh student@servera
   ...output omitted...
   [student@servera ~]$ sudo -i
   [sudo] password for student: student
   [root@servera ~]#
   ```

2. **Record `servera` hardware information**.

   2.1. View which boot image was last used.

   ```bash
   [root@servera ~]# dmesg | grep BOOT_IMAGE
   [    0.000000] Command line: BOOT_IMAGE=(hd0,gpt3)/boot/vmlinuz-4.18.0-305.el8.x86_64 root=/dev/vda3 ro no_timer_check net.ifnames=0 crashkernel=auto
   [    0.000000] Kernel command line: BOOT_IMAGE=(hd0,gpt3)/boot/vmlinuz-4.18.0-305.el8.x86_64 root=/dev/vda3 ro no_timer_check net.ifnames=0 crashkernel=auto
   ```

   2.2. Record the boot image in the `/root/systeminfo.txt` file.

   ```
   # Boot Image
   (hd0,gpt3)/boot/vmlinuz-4.18.0-305.el8.x86_64
   ```

   2.3. View the CPU model name.

   ```bash
   [root@servera ~]# lscpu
   Architecture: x86_64
   CPU op-mode(s): 32-bit, 64-bit
   Byte Order: Little Endian
   CPU(s): 2
   On-line CPU(s) list: 0,1
   Thread(s) per core: 1
   Core(s) per socket: 1
   Socket(s): 2
   NUMA node(s): 1
   Vendor ID: GenuineIntel
   BIOS Vendor ID: Red Hat
   Model name: Intel(R) Xeon(R) Gold 6248 CPU @ 2.50GHz
   ...output omitted...
   ```

   2.4. Record the CPU model name in the `/root/systeminfo.txt` file.

   ```
   # CPU Model
   Intel(R) Xeon(R) Gold 6248 CPU @ 2.50GHz
   ```

   2.5. View the size and form factor of the memory devices.

   ```bash
   [root@servera ~]# dmidecode -t memory
   # dmidecode 3.2
   Getting SMBIOS data from sysfs.
   SMBIOS 2.8 present.
   ...output omitted...
   Handle 0x1100, DMI type 17, 40 bytes
   Memory Device
       Size: 2 GB
       Form Factor: DIMM
       Locator: DIMM 0
       ...output omitted...
   ```

   2.6. Record the size and form factor of the memory devices in the `/root/systeminfo.txt` file.

   ```
   # Memory
   Size: 2 GB
   Form Factor: DIMM
   ```

   2.7. View the devices that are connected via any USB bus.

   ```bash
   [root@servera ~]# lsusb
   Bus 001 Device 002: ID 0627:0001 Adomax Technology Co., Ltd
   Bus 001 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
   ```

   2.8. Record the USB devices in the `/root/systeminfo.txt` file.

   ```
   # USB Devices
   Bus 001 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
   Bus 001 Device 002: ID 0627:0001 Adomax Technology Co., Ltd
   ```

   2.9. View the devices that are connected via the PCI bus.

   ```bash
   [root@servera ~]# lspci
   00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma] (rev 02)
   00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
   ...output omitted...
   ```

   2.10. Record the PCI devices in the `/root/systeminfo.txt` file.

   ```
   # PCI Devices
   00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma] (rev 02)
   00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
   ...output omitted...
   ```

3. **Install `rasdaemon`, start and enable the service, and add a summary of all hardware errors to the `/root/systeminfo.txt` file**.

   3.1. Install the `rasdaemon` package.

   ```bash
   [root@servera ~]# yum install rasdaemon
   ...output omitted...
   ```

   3.2. Start and enable the `rasdaemon` service.

   ```bash
   [root@servera ~]# systemctl enable --now rasdaemon
   Created symlink /etc/systemd/system/multi-user.target.wants/rasdaemon.service → /usr/lib/systemd/system/rasdaemon.service.
   ```

   3.3. View a summary of all hardware errors.

   ```bash
   [root@servera ~]# ras-mc-ctl --errors
   No Memory errors.
   No PCIe AER errors.
   No Extlog errors.
   No MCE errors.
   ```

   3.4. Record the error summary in the `/root/systeminfo.txt` file.

   ```
   # Hardware Errors
   No Memory errors.
   No PCIe AER errors.
   No Extlog errors.
   No MCE errors.
   ```

4. **Return to `workstation` as the student user**.

   ```bash
   [root@servera ~]# exit
   [student@servera ~]$ exit
   [student@workstation ~]$
   ```


