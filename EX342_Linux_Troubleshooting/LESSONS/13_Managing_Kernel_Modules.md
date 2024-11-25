# Lab 13: Managing Kernel Modules

## Objectives
- Manage kernel modules and parameters.

### Managing Kernel Modules
The running Linux kernel is constructed from feature and driver modules that can be loaded and unloaded on demand. Using kernel modules keeps the base kernel memory footprint small and ensures that code is loaded into memory only when required.

Most device drivers are built as kernel modules, as are file system drivers, encryption algorithms, and many other kernel features. Modules are found in the `/lib/modules/<KERNEL_VERSION>/kernel/<SUBSYSTEM>` directory as compressed `<KERNEL_MODULE_NAME>.ko.xz` object files.

Kernel modules have regular names and aliases. Aliases are used to provide shorter name alternatives or to standardize names for management routines to recognize. For example, USB devices create aliases in the form `usb:v0123d0001` to display the vendor ID `0123` and device ID `0001` in the device name. Similar letter patterns are standardized for all kernel modules.

### Viewing Loaded Modules
The `lsmod` command lists the currently loaded modules. The output includes the loaded module name, its size in bytes, the number of other modules that currently use this module, and an optional list of those other modules.

```bash
[user@host ~]$ lsmod
Module              Size  Used by
nft_fib_inet       16384  1
nft_fib_ipv4       16384  1 nft_fib_inet
nft_fib_ipv6       16384  1 nft_fib_inet
nft_fib            16384  3 nft_fib_ipv6,nft_fib_ipv4,nft_fib_inet
nft_reject         16384  1 nft_reject_inet
nft_ct             20480  10
nf_tables_set      49152  13
nft_chain_nat      16384  12
nf_nat             45056  1 nft_chain_nat
nfit               65536  0
libnvdimm         192512  1 nfit
kvm_intel         315392  0
kvm              847872  1 kvm_intel
...output omitted...
```

### Loading and Unloading Modules
Developers use the `insmod` and `rmmod` commands to load and unload modules. However, recommended practice for administrators is to use the `modprobe` command instead, which is more capable and handles module dependencies. The `modprobe` command also handles options or aliases that are defined in configuration files in `/etc/modprobe.d/`. To unload a module, use `modprobe -r`. A module in use cannot be unloaded until components that use that module are exited or detached.

With the `-v` option, `modprobe` can display the `insmod` and `rmmod` commands in use.

```bash
[root@host ~]$ modprobe mce-inject -v
insmod /lib/modules/4.18.0-305.el8.x86_64/kernel/arch/x86/kernel/cpu/mce/mceinject.ko.xz

[root@host ~]$ modprobe -r mce-inject -v
rmmod mce_inject
```

**Note**: The `modprobe` exit codes indicate whether the end result was achieved, rather than whether the action was performed. For example, when loading an already loaded module, `modprobe` returns true (exit code 0), even though no load action occurred.

### Viewing Module Parameters
Many kernel modules support optional parameters to alter their behavior. To view a module's parameters, use the `modinfo -p <MODULE>` command. Alternatively, most modules set their parameters within the `/sys/module/<MODULE_NAME>/parameters` directory, with each parameter as a separate file. If a parameter is modifiable at runtime, the corresponding file is root writable. If a parameter is configurable only at module load time, the corresponding file is read-only.

To view the `kvm` module's parameters, use `modinfo -p`. The output includes parameter names, descriptions, and value types, such as integer, Boolean, or string.

```bash
[root@host ~]# modinfo -p kvm
tdp_mmu: (bool)
nx_huge_pages: (bool)
nx_huge_pages_recovery_ratio: (uint)
flush_on_reuse: (bool)
ignore_msrs: (bool)
report_ignored_msrs: (bool)
min_timer_period_us: (uint)
kvmclock_periodic_sync: (bool)
tsc_tolerance_ppm: (uint)
lapic_timer_advance_ns: (int)
vector_hashing: (bool)
enable_vmware_backdoor: (bool)
force_emulation_prefix: (bool)
pi_inject_timer: (bint)
halt_poll_ns: (uint)
halt_poll_ns_grow: (uint)
halt_poll_ns_grow_start: (uint)
halt_poll_ns_shrink: (uint)
```

You can view the same `kvm` module parameters with the `/sys/module/kvm/parameters` directory.

```bash
[root@host ~]# ls -l /sys/module/kvm/parameters/
total 0
-r--r--r--. 1 root root 4096 Sep 24 12:23 enable_vmware_backdoor
-rw-r--r--. 1 root root 4096 Sep 24 12:23 flush_on_reuse
...output omitted...
```

### Managing Module Parameters
To pass a parameter to a module, add the `parameter=value` option to the `modprobe` command when loading the module. This example sets the `buffer_kbs` parameter to `64` for the `st` module:

```bash
[root@host ~]# modprobe -v st buffer_kbs=64
insmod /lib/modules/3.10.0-327.el7.x86_64/kernel/drivers/scsi/st.ko buffer_kbs=64
```

To set this parameter each time that the module is loaded, use a configuration file in the `/etc/modprobe.d/` directory. Module configuration files require that file names end in `.conf`. Files are parsed in alphanumeric order. Add parameters with an `options` line.

```
options st buffer_kbs=64 max_sg_segs=512
```

Group options into files that are named for their module, such as `/etc/modprobe.d/st.conf`, or into files that relate to a function, such as `/etc/modprobe.d/throughput.conf`.


