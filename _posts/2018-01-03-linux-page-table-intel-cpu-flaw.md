---
layout:     post
title:      Linux Page Table and Intel CPU Flaw
date:       2018-01-03
summary:    A short summary for understanding Linux page table and the Intel CPU flaw
categories: Operating Systems
---

### 1. Current Linux Page Table
* The page table for every user program contains not only user space memory mappings but also kernel space memory mappings.
* This is for improving performance when doing syscall, interrupts and etc. Because if user space and kernel space memory are in the same page table, there is no need to change CPU cached data (for instance, no need to flush TLB on context switch), which is a huge performance improvement.
* The kernel space mappings on the page table is protected so that the user program cannot directly access them.

### 2. ASLR and KASLR (for 64 bits OS)
* It turns out that leaking the memory mappings of kernel space is not secure. So ASLR, Address Space Layout Randomization, is introduced, where the mappings of the kernel space in the page table are distributed randomly so that it is unlikely for the attacker to know what is the memory mapping to the kernel address.
* KASLR is simply the ASLR applied on the kernel at system boot time.

### 3. KPTI (previously called KAISER)
* It turns out even the ASLR is not secure enough, there are several ways to attack it with the current Intel CPU design (for instance, speculatively execute code potentially without performing security checks? details sources: https://www.theregister.co.uk/2018/01/02/intel_cpu_design_flaw/ and https://lwn.net/Articles/738975/) .
* So KPTI (Kernel page-table isolation) is introduced. The idea is to split the page table into two copies: one contains both kernel space mapping and userspace mapping, the other contains only user space mapping (with minimum amount of kernel space information). When running in user mode, only the second copy is active. When switching into kernel mode, the first copy will be active.
* This is expensive, because the context switch between user space and kernel space will have to flush TLB. This patches set is reported to introduce a slowdown of 5% to 30% (data is gathered at the current date) for all applications.


