---
title: "kargs | secureblue"
description: "An overview of the hardened boot kargs used in secureblue"
permalink: /articles/kargs
---

# Table of contents

{: #table-of-contents}

- [Introduction](#introduction)
- [Standard](#standard)
- [Additional](#additional)
  - [Disable 32-bit processes and syscalls](#32bit)
  - [Force disable simultaneous multithreading](#smt)
  - [Unstable kargs](#unstable)

# Introduction

On secureblue and other systems that use `rpm-ostree`, kernel arguments (kargs)
can be managed using `rpm-ostree kargs`. Run `rpm-ostree kargs --help` for usage
information.

Secureblue sets some kernel arguments by default, and provides a script
`ujust set-kargs-hardening` that provides additional sets of kernel arguments.
To remove all kernel arguments that secureblue adds, you can run
`ujust remove-kargs-hardening`.

For details on what each kernel argument does, see
[the kernel documentation](https://www.kernel.org/doc/html/v6.17/admin-guide/kernel-parameters.html).

# Standard

Stable kernel arguments that are set by default on a fresh secureblue
installation, and are always applied by the script `ujust set-kargs-hardening`.

- `hash_pointers=always`: Hash kernel pointers even if `slab_debug` is enabled.
- `init_on_alloc=1`: Fill newly allocated pages and heap objects with zeroes,
  mitigating use-after-free vulnerabilities.
- `init_on_free=1`: Fill freed pages and heap objects with zeroes, mitigating
  use-after-free vulnerabilities.
- `iommu=force` and `intel_iommu=on`: Mitigate DMA attacks by enabling IOMMU.
- `iommu.passthrough=0`: Disable IOMMU bypass.
- `iommu.strict=1`: Synchronously invalidate IOMMU hardware TLBs.
- `kvm_amd.sev=1`, `kvm_amd.sev_es=1`, `kvm_amd.sev_snp=1`: Enable AMD Secure
  Encrypted Virtualization (SEV) and extensions.
- `kvm-intel.vmentry_l1d_flush=always`: Enable unconditional flushes, required
  for complete L1D vulnerability mitigation.
- `kvm.mitigate_smt_rsb=1`: Mitigate cross-thread return address predictions
  vulnerability for certain KVM hypervisor configurations.
- `l1d_flush=on`: Enable the mechanism to flush the L1D cache on context switch.
- `l1tf=full,force`: Force enable all available mitigations for the L1TF
  vulnerability.
- `lockdown=confidentiality`: Enable kernel lockdown in the strictest mode.
- `loglevel=0`: Only log level 0 (system is unusable) messages to the console.
- `mitigations=auto,nosmt`: Automatically mitigate all known CPU
  vulnerabilities, including disabling SMT if necessary.
- `module.sig_enforce=1`: Only allow kernel modules that have been signed with a
  valid key to be loaded.
- `page_alloc.shuffle=1`: Enable page allocator freelist randomization, reducing
  page allocation predictability.
- `proc_mem.force_override=ptrace`: Only allow memory permissions for
  `/proc/<pid>/mem` to be overridden by active ptracers.
- `pti=on`: Enable kernel page table isolation.
- `random.trust_bootloader=off`: Disable trusting the use of the seed passed by
  the bootloader.
- `random.trust_cpu=off`: Disable CPU-based entropy sources, as it's not
  auditable and has resulted in vulnerabilities.
- `randomize_kstack_offset=on`: Randomize kernel stack offset on each syscall,
  making certain types of attacks more difficult.
- `rd.shell=0`, `rd.emergency=halt`: Mitigate
  [initramfs malware injection attack](https://insinuator.net/2025/07/insecure-boot-injecting-initramfs-from-a-debug-shell/).
- `slab_debug=FZ`: Enable sanity checks and red zoning for the
  [kernel slab allocator](https://www.kernel.org/doc/html/latest/admin-guide/mm/slab.html).
- `slab_nomerge`: Disable the merging of slabs, increasing difficulty of heap
  exploitation.
- `spec_store_bypass_disable=on`: Disable spec store bypass for all programs.
- `spectre_v2=on`: Turn on spectre_v2 mitigations at boot time for all programs.
- `ssbd=force-on`: Enable mitigation for Speculative Store Bypass vulnerability
  for both kernel and userspace on vulnerable CPUs.
- `systemd.ssh_auto=no`: Disable automatic creation of socket-activated SSH
  server by systemd (see
  [systemd-ssh-generator(8)](https://man7.org/linux/man-pages/man8/systemd-ssh-generator.8.html)
  for details), which can lead to
  [security vulnerabilities](https://blog.nsrun.io/2026/01/15/systemd-vsock-openssh-server/).
- `vdso32=0`: Disable 32-bit vDSO.
- `vsyscall=none`: Disable vsyscall as it is both obsolete and enables an ROP
  attack vector.

# Additional

Sets of additional kargs that can be selectively set alongside the standard
kargs detailed above. The `set-kargs-hardening` command prompts the user on
whether to add apply of the 3 sets of kargs detailed below:

## Disable 32-bit processes and syscalls

{: #32bit}

{% include alert.html type='note' content='32-bit support is needed by some legacy software, such as Steam.' %}

- `ia32_emulation=0`: Disables 32-bit processes and syscalls.

## Force disable simultaneous multithreading

{: #smt}

- `nosmt=force`: Disables this hardware feature on user request, regardless of
  whether it is affected by known vulnerabilities. Note that this
  [halves the number of CPU cores](/faq#smt).

## Unstable kargs

{: #unstable}

{% include alert.html type='caution' content='These may cause issues on some hardware.' %}

- `amd_iommu=force_isolation`: Mitigate DMA attacks on AMD hardware by having
  the AMD IOMMU driver force device isolation for all devices.
- `bdev_allow_write_mounted=0`: Disallow direct writes to filesystem block
  devices.
- `debugfs=off`: Disable debugfs to prevent exposure of sensitive kernel
  information.
- `efi=disable_early_pci_dma`: Fill IOMMU protection gap by setting the
  BUSMASTER bit during early boot.
- `gather_data_sampling=force`: Mitigate unprivileged speculative access to data
  by using the microcode mitigation when available, or by disabling AVX on
  affected systems where the microcode hasn’t been updated to include the
  mitigation.
- `mem_encrypt=on`: Enable AMD Secure Memory Encryption (SME).
- `oops=panic`: Treat [oopses](https://en.wikipedia.org/wiki/Linux_kernel_oops)
  as fatal, preventing attacks that depend on repeated oopses.
