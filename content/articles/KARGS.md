---
title: "kargs | secureblue"
description: "An overview of the hardened boot kargs used in secureblue"
permalink: /articles/kargs
---

# Table of contents
{: #table-of-contents}

- [Standard](#standard)
- [Additional](#additional)
  - [Disable 32-bit processes and syscalls](#32-bit)
  - [Force disable simultaneous multithreading](#smt)
  - [Unstable kargs](#unstable)

# Standard

Stable kargs that are always applied by the `set-kargs-hardening` ujust script.

- `init_on_alloc=1`: Zero newly allocated pages and heaps, mitigating use-after-free vulnerabilities.
- `init_on_free=1`: Fills freed pages and heaps with zeroes, mitigating use-after-free vulnerabilities.
- `iommu=force` and `intel_iommu=on`: Mitigate DMA attacks by enabling IOMMU.
- `iommu.passthrough=0`: Disable IOMMU bypass.
- `iommu.strict=1`: Synchronously invalidate IOMMU hardware TLBs.
- `kvm-intel.vmentry_l1d_flush=always`: Enables unconditional flushes, required for complete l1d vulnerability mitigation.
- `l1d_flush=on`: Enable the mechanism to flush the L1D cache on context switch.
- `l1tf=full,force`: Force enables all available mitigations for the L1TF vulnerability.
- `lockdown=confidentiality`: Enable kernel lockdown in the strictest mode.
- `mitigations=auto,nosmt`: Automatically mitigate all known CPU vulnerabilities, including disabling SMT if necessary.
- `module.sig_enforce=1`: Only allows kernel modules that have been signed with a valid key to be loaded.
- `oops=panic`: Treat [oopses](https://en.wikipedia.org/wiki/Linux_kernel_oops) as fatal, preventing attacks that depend on repeated oopses.
- `page_alloc.shuffle=1`: Enables page allocator freelist randomization, reducing page allocation predictability.
- `pti=on`: Enable kernel page table isolation.
- `randomize_kstack_offset=on`: Randomize kernel stack offset on each syscall, making certain types of attacks more difficult.
- `random.trust_bootloader=off`: Disable trusting the use of the seed passed by the bootloader.
- `random.trust_cpu=off`: Disable CPU-based entropy sources, as it's not auditable and has resulted in vulnerabilities.
- `rd.shell=0`, `rd.emergency=halt`: Prevent [initramfs malware injection](https://insinuator.net/2025/07/insecure-boot-injecting-initramfs-from-a-debug-shell/)
- `slab_nomerge`: Disables the merging of slabs, increasing difficulty of heap exploitation.
- `spec_store_bypass_disable=on`: Disable spec store bypass for all programs.
- `spectre_v2=on`: Turn on spectre_v2 mitigations at boot time for all programs.
- `vsyscall=none`: Disable vsyscall as it is both obsolete and enables an ROP attack vector.

# Additional

Sets of additional kargs that can be selectively set alongside the standard kargs detailed above. The `set-kargs-hardening` command prompts the user on whether to add apply of the 3 sets of kargs detailed below:

## Disable 32-bit processes and syscalls
{: #32-bit}

{% include alert.html type='note' content='32-bit support is needed by some legacy software, such as Steam.' %}

- `ia32_emulation=0`: Disables 32-bit processes and syscalls.

## Force disable simultaneous multithreading
{: #smt}

- `nosmt=force`: Disables this hardware feature on user request, regardless of whether it is affected by known vulnerabilities

## Unstable kargs
{: #unstable}

{% include alert.html type='caution' content='These may cause issues on some hardware.' %}

- `efi=disable_early_pci_dma`: Fill IOMMU protection gap by setting the BUSMASTER bit during early boot
- `debugfs=off`: Disable debugfs to prevent exposure of sensitive kernel information
- `gather_data_sampling=force`: Mitigate unprivileged speculative access to data by using the microcode mitigation when available, or by disabling AVX on affected systems where the microcode hasn’t been updated to include the mitigation.
- `amd_iommu=force_isolation`: Mitigate DMA attacks by enabling IOMMU.
