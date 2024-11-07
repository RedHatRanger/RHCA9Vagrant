# LESSON 12: Identifying Hardware Issues

## Objectives

- Identify system hardware devices and hardware issues.

### Reviewing Kernel Messages

A simple and direct source of hardware information is the running Linux kernel, which functions as the mediator of all hardware access. The kernel exposes information structures through the `/proc` and `/sys` file systems, as well as by sending kernel messages and events.

Kernel messages are written to a preallocated ring buffer that is known as the `dmesg` buffer. A ring buffer is a sequential memory structure where data overflow starts again at the top of the buffer. Over time, recent messages fill the buffer and overwrite original messages, but the buffer never grows in size.

Examine the `dmesg` buffer for the following reasons:
- Reviewing hardware that was detected at boot time.
- Observing driver messages that are sent as hardware is detected, attached, or detached.
- Observing warning or error messages as events occur.

To display ring buffer messages, use the `dmesg` or the `journalctl -k` command. The `journald` service can be configured for persistent storage, which is preferred over using the `/var/log/dmesg` file pointer to the ring buffer. The `systemd-journal` service captures the kernel ring buffer. The `rsyslog` service retrieves information from the kernel ring buffer with the `imjournal` plug-in.

### Exploring Kernel Messages

The first two lines of the `dmesg` command contain the kernel version and kernel parameters that are used on the last system boot, which is useful for troubleshooting boot issues.

```bash
[root@host ~]# dmesg | head -n2
[    0.000000] Linux version 4.18.0-305.el8.x86_64 (mockbuild@x86-vm-07.build.eng.bos.redhat.com) (gcc version 8.4.1 20200928 (Red Hat 8.4.1-1) (GCC)) #1 SMP Thu Apr 29 08:54:30 EDT 2021
[    0.000000] Command line: BOOT_IMAGE=(hd0,gpt3)/boot/vmlinuz-4.18.0-305.el8.x86_64 root=/dev/vda3 ro no_timer_check net.ifnames=0 crashkernel=auto
```

Because of the quantity and complexity of `dmesg` log entries, view the log with filters to focus on relevant information. This example displays the memory that was made available during booting.

```bash
[root@host ~]# dmesg | grep "Memory"
[    0.000000] Memory: 261668K/2096600K available (12292K kernel code, 2100K rwdata, 3816K rodata, 2348K init, 3320K bss, 271600K reserved, 0K cma-reserved)
[    0.108065] x86/mm: Memory block size: 128MB
```

You can filter messages by their syslog facility and severity by using the `-f` and `-l` options.

```bash
[root@host ~]# dmesg -f kern -l warn
[    0.025358] acpi PNP0A03:00: fail to add MMCONFIG information, can't access extended PCI configuration space under this bridge.
[    6.095391] printk: systemd: 16 output lines suppressed due to ratelimiting
```

Make message timestamps easier to read by using the `-T` option.

```bash
[root@host ~]# dmesg -f kern -l warn -T
[Wed Sep 22 07:17:39 2021] acpi PNP0A03:00: fail to add MMCONFIG information, can't access extended PCI configuration space under this bridge.
[Wed Sep 22 07:17:45 2021] printk: systemd: 16 output lines suppressed due to ratelimiting
```

### Identifying CPU Capabilities

Modern systems typically have multiple CPUs, each with multiple cores per socket, and possibly multiple hyper-threads per core, each with different levels of local and shared caches. The `lscpu` command provides a quick summary of the local configuration. The following example shows `lscpu` output and an explanation of the pertinent information.

```bash
[root@host ~]# lscpu
Architecture:           x86_64
CPU op-mode(s):         32-bit, 64-bit
Byte Order:             Little Endian
CPU(s):                 4
On-line CPU(s) list:    0-3
Thread(s) per core:     1
Core(s) per socket:     4
Socket(s):              1
NUMA node(s):           1
Vendor ID:              GenuineIntel
CPU family:             6
Model:                  23
Model name:             Intel(R) Core(TM)2 Quad CPU Q9550 @ 2.83GHz
Stepping:               10
CPU MHz:                2833.000
BogoMIPS:               5665.57
Virtualization:         VT-x
L1d cache:              32K
L1i cache:              32K
L2 cache:               6144K
NUMA node0 CPU(s):      0-3
Flags:                  fpu vme vmx ...
```

- **Architecture**: The architecture of the CPU.
- **CPU(s)**: The number of logical cores that are available to the kernel for task scheduling.
- **Thread(s) per core**: The number of hyper-threads per core.
- **Core(s) per socket**: The number of cores per socket.
- **Socket(s)**: The number of physical sockets.
- **NUMA node(s)**: The number of distinct memory buses on NUMA systems.
- **Virtualization**: The hardware-assisted virtualization support for the CPU.
- **Caches**: The sizes of the relative caches.
- **NUMA node CPU(s)**: The mapping of the logical CPUs to the NUMA address buses.
- **Flags**: The list of extended technologies that the CPU supports.

### Identifying Memory

Use the `dmidecode -t memory` command to retrieve information about physical memory banks, including the type, speed, and location.

```bash
[root@host ~]# dmidecode -t memory
# dmidecode 2.12
...output omitted...
Handle 0x0007, DMI type 17, 34 bytes
Memory Device
    Array Handle: 0x0005
    Error Information Handle: Not Provided
    Total Width: 64 bits
    Data Width: 64 bits
    Size: 8192 MB
    Form Factor: SODIMM
    Locator: ChannelA-DIMM1
    Bank Locator: BANK 1
    Type: DDR3
    Speed: 1600 MHz
    Manufacturer: Hynix/Hyundai
    Serial Number: 0E80EABA
    Part Number: HMT41GS6BFR8A-PB
...output omitted...
```

### Identifying Storage Devices

To identify physical storage devices, use the `lsscsi` command to list the physical SCSI driver-compatible devices that are attached to the system, including SSD, USB, SATA, and SAS disks.

```bash
[root@host ~]# lsscsi -v
[0:0:0:0]    disk    ATA      SAMSUNG MZ7LN512  4L0Q  /dev/sda
    dir: /sys/bus/scsi/devices/0:0:0:0 [/sys/devices/pci0000:00/0000:00:1f.2/ata1/host0/target0:0:0/0:0:0:0]
```

The `hdparm` command can provide detailed information for individual storage devices.

```bash
[root@host ~]# hdparm -I /dev/sda
/dev/sda:
    ATA device, with non-removable media
    Model Number:       SAMSUNG MZ7LN512HCHP-000L1
    Serial Number:      S1ZKNXAG806853
    Firmware Revision:  EMT04L0Q
...output omitted...
```

### Identifying Peripherals on the USB and PCI Buses

The `lspci` and `lsusb` commands query system buses to discover connected, active peripherals. The `lspci` command displays hardware devices that are connected to the system PCI bus, while the `lsusb` command displays hardware devices connected to any USB bus.

```bash
[root@host ~]# lspci
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v3/4th Gen Core Processor DRAM Controller (rev 06)
00:01.0 PCI bridge: Intel Corporation Xeon E3-1200 v3/4th Gen Core Processor PCI Express x16 Controller (rev 06)
...output omitted...
```

```bash
[root@host ~]# lsusb
Bus 001 Device 009: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 001 Device 008: ID 17a0:0001 Samson Technologies Corp. C01U condenser microphone
...output omitted...
```

### Hardware Error Reporting

Red Hat Enterprise Linux 8 provides the `rasdaemon` daemon to report hardware errors. The `rasdaemon` service processes reliability, availability, and serviceability (RAS) error events generated by kernel tracing. These trace events are logged in `/sys/kernel/debug/tracing` and are reported by `rsyslog` and `journald`.

To use `rasdaemon`, install the package, and then start and enable the service.

```bash
[root@host ~]# yum install rasdaemon
[root@host ~]# systemctl enable --now rasdaemon
```

The `ras-mc-ctl` command from the `rasdaemon` package works with error detection and correction (EDAC) drivers to provide a summary or list of errors.

### Memory Testing

When physical memory is suspected of causing errors or being damaged, you can run a thorough, repetitive memory test with the `memtest86+` package. Because live memory testing on an active system is not recommended or might cause instability, `memtest86+` installs a separate boot entry that loads a bootable program instead of a regular Linux kernel.

1. Install the `memtest86+` package.

   ```bash
   [root@host ~]# yum install memtest86+
   ```

2. Run the `memtest-setup` command to add a new template to the `/etc/grub.d/` directory.

   ```bash
   [root@host ~]# memtest-setup
   ```

3. Update the grub2 boot loader configuration.

   ```bash
   [root@host ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

