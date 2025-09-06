## RISC-V physical memory protection (PMP)
> In simple terms, PMP is an ISA feature in RISC-V processors that allows one to divide the system physical memory into (contiguous) regions with specific access permissions (i.e., RWX). For example, 0x0000-0x1000: R-X, 0x1000-0x2000: RW-, 0x2000-0x2100: M-mode only, 0x2100-0xFFFF: no access. 


## Technical stuff on PMP
RISC-V defines execution modes or privilege levels: machine mode (`M-mode`) is the highest privilege level and houses the firmware. M-mode has access to all memory regions and has the privilege to change machine registers. The supervisor-mode (`S-mode`) has lower privileges than the M-mode and houses the OS. Applications execute in user-mode (`U-mode`). PMP provides the possibility to isolate a user application such that its memory is inaccessible to the OS or other applications. M-mode is in charge of configuring the PMP registers and enforcing the PMP permissions.

In order to protect a certain region of physical memory, we need to define the start/end address and the permissions that apply to them. RISC-V provides two types of registers control and status registers (CSRs) for PMP: `pmpaddr` for encoding the memory address (usually the end address) of a PMP regions, and `pmpcfg` for encoding the permission. Both define a PMP entry. We can have a maximum of 64 PMP entries.
## Hardware setup




## Software setup





























## Other resources and further reading

- [Keystone: An Open Framework for Architecting TEEs](https://arxiv.org/pdf/1907.10119)
- [DORAMI: Privilege Separating Security Monitor on RISC-V TEEs](https://www.usenix.org/system/files/usenixsecurity25-kuhne.pdf)
- [Adding Physical Memory Protection to the VeeR EL2 RISC-V Core](https://riscv.org/blog/2024/03/adding-physical-memory-protection-to-the-veer-el2-risc-v-core-2/)
- [YouTube video explaining PMP](https://www.youtube.com/watch?v=86kIwpPDsOU)
- [RISC-V ISA specifications](https://riscv.org/specifications/ratified/)
- [Configuring PMP](https://www.youtube.com/watch?v=cWlEKpCtjes)