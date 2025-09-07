## RISC-V physical memory protection (PMP)
> In simple terms, PMP is an ISA feature in RISC-V processors that allows one to divide the system physical memory into (contiguous) regions with specific access permissions (i.e., RWX). It is configured by the most privileged software (firmware) to create and enforce rules that prevent less privileged software (like an operating system or applications) from reading, writing, or executing code in specific memory regions. 


## Technical stuff on PMP
RISC-V defines execution modes or privilege levels: machine mode (`M-mode`) is the highest privilege level and houses the firmware. M-mode has access to all memory regions and has the privilege to change machine registers. The supervisor-mode (`S-mode`) has lower privileges than the M-mode and houses the OS. Applications execute in user-mode (`U-mode`). PMP provides the possibility to isolate a user application such that its memory is inaccessible to the OS or other applications. M-mode is in charge of configuring the PMP registers and enforcing the PMP permissions.

In order to protect a certain region of physical memory, we need to define the start/end address and the permissions that apply to them. RISC-V provides two types of registers control and status registers (CSRs) for PMP: `pmpaddr` for encoding the memory address (usually the end address) of a PMP regions, and `pmpcfg` for encoding the permission. Both define a PMP entry. We can have a maximum of 64 PMP entries.



## Hardware setup
We will focus on two RISC-V boards.
1. **[VisionFive 2](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf)**: a RISC-V based computer from StarFive with an integrated 3D GPU, capable of running a full Linux OS. It is very similar to a Raspberry Pi. Follow steps in [this readme](./visionfive2.md) to setup this board and test PMP.
2. **[ESP32-C3](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/get-started/index.html)**: a very low cost (~20 USD) SoC that integrates WiFi, BlueTooth, and multiple peripherals (I2S, I2C, UART, GPIO). It is very low power and not meant to run a full Linux OS. Nevertheless it provides excellent documentation to test features like PMP. Follow steps in [this readme](./esp32-c3.md) to setup and test PMP on ESP32-C3.

- Note: my PMP testing on these RISC-V is still ongoing. It's a bit tricky ...



## Other resources and further reading

- [Keystone: An Open Framework for Architecting TEEs](https://arxiv.org/pdf/1907.10119)
- [DORAMI: Privilege Separating Security Monitor on RISC-V TEEs](https://www.usenix.org/system/files/usenixsecurity25-kuhne.pdf)
- [Adding Physical Memory Protection to the VeeR EL2 RISC-V Core](https://riscv.org/blog/2024/03/adding-physical-memory-protection-to-the-veer-el2-risc-v-core-2/)
- [YouTube video explaining PMP](https://www.youtube.com/watch?v=86kIwpPDsOU)
- [RISC-V ISA specifications](https://riscv.org/specifications/ratified/)
- [Configuring PMP](https://www.youtube.com/watch?v=cWlEKpCtjes)
- [Memory protection for embedded RISC-V systems](https://ntnuopen.ntnu.no/ntnu-xmlui/bitstream/handle/11250/3012737/no.ntnu:inspera:112046434:20987266.pdf?sequence=1)