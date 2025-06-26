This configuration, which I developed to enhance the security of Securonis, is a basic hardening setup and a fork of Kicksecure’s security-misc package. 
However, I made significant modifications to improve performance and optimize it for daily use.
This configuration provides basic to intermediate level protection rather than strict security.
Thus, it aims to reduce issues for users during everyday usage. 
These settings are quite comprehensive for a distribution focused on daily use and aim to offer a reasonable balance between security and performance.
 

---------------------------------------------------------------------------------------------------



# Kernel and System Security Configuration

## 1. Kernel Space Protection Mechanisms

### Memory Protection Measures
- **slab_nomerge:** Prevents merging of similarly sized memory slabs, providing protection against heap overflow attacks.  
- **init_on_alloc=1** and **init_on_free=1:** Zero out newly allocated or freed memory pages to protect against use-after-free attacks.  
- **page_alloc.shuffle=1:** Enables the page allocator to randomize free lists, making memory predictions harder and blocking ROP (Return-Oriented Programming) attacks.  

### Security Measures
- **pti=on:** Protects against Kernel ASLR (Address Space Layout Randomization) bypasses and mitigates the Meltdown CPU vulnerability.  
- **randomize_kstack_offset=on:** Randomizes kernel stack offset during system calls, making memory corruption attacks harder to predict.  
- **vsyscall=none:** Disables vsyscalls since they reside at fixed memory addresses, reducing the attack surface.  
- **kfence.sample_interval=100:** Kernel Electric-Fence memory safety mechanism detects various memory errors with low overhead.  
- **vdso32=0:** Disables 32-bit Virtual Dynamic Shared Object (vDSO) mappings to reduce the attack surface.  

---

## 2. Direct Memory Access (DMA) Protections
- **amd_iommu=on** and **intel_iommu=on:** Enable CPU vendor-specific IOMMU drivers to reduce DMA attacks.  
- **iommu=force, iommu.passthrough=0, iommu.strict=1:** Enforce IOMMU translation to ensure devices never access stale data.  
- **efi=disable_early_pci_dma:** Clears busmaster bits on all PCI bridges during EFI handover to terminate all ongoing DMA before kernel IOMMU setup.  

---

## 3. Entropy and Randomness Improvements
- **random.trust_bootloader=off** and **random.trust_cpu=off:** Do not credit entropy from CPU or bootloader during boot, reducing dependency on closed-source or potentially unreliable RNGs.  
- **extra_latent_entropy:** Obtains additional entropy from the first 4GB of RAM when the runtime memory allocator initializes.  
- **jitterentropy_rng:** Loads the jitter entropy RNG module to increase system entropy.  

---

## 4. CPU Vulnerability Mitigations
- **spectre_v2=on** and **spectre_bhi=on:** Protect against Spectre Variant 2 (branch target injection) and Intel branch history injection vulnerabilities.  
- **spec_store_bypass_disable=on** and **ssbd=force-on:** Mitigate Spectre Variant 4 by disabling speculative store bypass system-wide for kernel and userspace.  
- **l1tf=full,force:** Provide protection against L1 Terminal Fault (L1TF) by disabling runtime L1D flush control.  
- **mds=full,nosmt:** Mitigate Microarchitectural Data Sampling (MDS) by flushing CPU buffers.  
- **tsx=off** and **tsx_async_abort=full,nosmt:** Disable TSX to mitigate TSX Asynchronous Abort (TAA) vulnerability.  
- **retbleed=auto,nosmt:** Apply CPU-specific mitigation for the Retbleed vulnerability.  

---

## 5. Hardware Information Hiding
- **hide-hardware-info:** Hides hardware identifiers from normal users in `/sys`, `/proc/cpuinfo`, `/proc/bus`, and `/proc/scsi`, securing the system.  
- **remove-system.map:** Removes the kernel’s System.map file to make it harder for attackers to discover kernel symbol addresses.  

---

## 6. Network Security
- **nf_conntrack_helper=0:** Disables automatic assignment of Netfilter connection tracking helpers, reducing kernel attack surface.  
- **ipv6.privacy-extensions=on:** Enables IPv6 privacy extensions for better network privacy.  


