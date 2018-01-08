---
layout:     post
title:      Linux Page Table with KPTI patches and Intel CPU Flaw
date:       2018-01-03
summary:    A short summary for understanding Linux Page Table with KPTI patches and the Intel CPU flaw
categories: Operating Systems
---

### 1. Current Linux Page Table
* The page table for every user program contains not only user space memory mappings but also kernel space memory mappings.
* This is for improving performance when doing syscall, interrupts and etc. Because if user space and kernel space memory are in the same page table, there is no need to change CPU cached data (for instance, no need to flush TLB (or PCID) on context switch), which is a huge performance improvement.
* The kernel space mappings on the page table is protected so that the user program cannot directly access them.

### 2. ASLR and KASLR (for 64 bits OS)
* It turns out that leaking the memory mappings of kernel space is not secure. So ASLR, Address Space Layout Randomization, is introduced, where the mappings of the kernel space in the page table are distributed randomly so that it is unlikely for the attacker to know what is the memory mapping to the kernel address.
* KASLR is simply the ASLR applied on the kernel at system boot time.
* It turns out the ASLR and KASLR are not secure enough, there are still several ways for the attacker to guess the kernel memory mappings, it is more like a last-line of defense. But if the CPU and OS can prevent user program from authorized accessing those memory mappings, it is still OK.

### 3. Speculative Execution (The Cause of Security Issue of Intel CPU)
* Why it is not OK now?
* Speculative (or out-of-order) Execution is implemented in lots of modern CPUs (including both AMD's and Intel's models). The idea of this technology is to accelerate the sequential executions by pre-execute some following instructions while executing current one and perform the checks later to see if the results of those pre-executed instructions are needed or not. The issue with this feature is that even if the results of those pre-executed instructions are discarded later after checking, the results are still kept in the CPU cache.
* It turns that with the speculative execution, the attacker can perform unauthorized access to kernel memory on current Intel's CPU (AMD CPU seems to avoid speculative execution on crucial memory addresses) and read those information into CPU cache. Though the attacker cannot read the information directly from the cache, but by carefully timing the right instruction (instruction reading content from cache is much faster then reading it from RAM), the attacker can still guess the content of the kernel memory.


### 4. KPTI (previously called KAISER)
* So KPTI (Kernel page-table isolation) is introduced. The idea is to split the page table into two copies: one contains both kernel space mapping and userspace mapping, the other contains only user space mapping (with minimum amount of kernel space information). When running in user mode, only the second copy is active. When switching into kernel mode, the first copy will be active.
* This is expensive, because the context switch between user space and kernel space will have to flush TLB (or PCID). This patches set is reported to introduce a slowdown of 5% to 30% (data is gathered at the current date) for all applications.


