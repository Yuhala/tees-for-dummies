## Setting up OP-TEE on STM32MP157D-DK1
As promised, the OP-TEE documentation provides instructions on [setting up OP-TEE on STM32MP157D-DK1](https://optee.readthedocs.io/en/latest/building/devices/stm32mp1.html). The following guide is based on this documentation. We will build OP-TEE for the STM32MP157D-DK1 and flash it on the board. Note: the following steps are done on your work/host PC (not the board/device).

1. **Install prerequisites**: Install the prerequisites as shown [here](https://optee.readthedocs.io/en/latest/building/prerequisites.html#prerequisites). We provide a script [install_prerequisites.sh]() for Ubuntu 22.04.
```bash
./install_prerequisites.sh
```
2. **Install Android repo tool**: `repo` is a Python wrapper script developed by Google to manage multiple Git repositories at once. We need it for building OP-TEE. We equally provide a script here for this purpose.
```bash
./install_repo.sh
```
3. **Get OP-TEE for STM32MP1**: Check [the Manifest XML file](https://optee.readthedocs.io/en/latest/building/gits/build.html#current-version) corresponding to the board. This manifest sets up the necessary software components for running OP-TEE on the given board/device. Ours is `stm32mp1.xml`.
```bash
mkdir -p optee-stm32mp1 & cd optee-stm32mp1
repo init -u https://github.com/OP-TEE/manifest.git -m stm32mp1.xml 
repo sync 
```
4. **Build the toolchains**: Build the cross-compilation toolsets needed to build software for the target hardware architecture.
```bash
cd optee-stm32mp1/build
make -j2 toolchains
```
5. **Build the solution**: This is the core compilation step where all the downloaded source code components (OP-TEE OS, Linux kernel, Trusted Firmware-A, U-Boot, xtest, the root filesystem, etc.) are compiled together to create a complete, bootable software stack for your chosen platform. 
<!-- When following the official documentation, for our chosen board, the platform used in the build should be `PLATFORM=stm32mp1-157A_DK1`. However, after building and testing on the board, I had issues starting tee-supplicant and missing `/dev/tee`. After reading some GitHub issues on a similar subject, this problem can be avoided by simply not specifying the `PLATFORM` in the make instruction, which we do below.-->
> Below build command is being debugged as there is an issue with OP-TEE after booting into the built kernel. So, as of now, this README is incomplete.
```bash
make -j `nproc` PLATFORM=stm32mp1-157A_DK1 all 
```
This step takes some time. If you encounter build issues, you can pipe the build to a log file and check for errors. In this case, you should also avoid the `-j` flag for multi-threaded build so the log results are understandable. 

When the build completes, it generates an image file `sdcard.img` in the `../out/bin/` from build root path. The image is a GPT multipartition image you can copy to the target SD-card.

6. **Copy image to SD card**: Insert the SD-card into your work PC and search for its path with `lsblk`; copy the generated `sdcard.img` to the card using the commands below. 
```bash
sudo dd if=../out/bin/sdcard.img of=/dev/sdX conv=fdatasync status=progress
sudo sgdisk -e /dev/sdX
```
The command `sgdisk -e /dev/sdX` converts the partition table on the device `/dev/sdX` from MBR (Master Boot Record) format to GPT (GUID Partition Table) format, while attempting to preserve the existing partition data. 

Once the copy is complete, insert the SD-card into the board.

7. **Acess the serial console and boot the board**: Now, we will try to access a serial console, which uses a UART port to communicate with other devices (e.g., your PC). On the STM32MP157D-DK1, this serial console is mapped to UART4 by default. UART4 is connected to the ST-LINK debugger chip on the development board, and handles serial-to-USB translation. Connect a USB micro cable from your PC to the `ST-LINK CN11` port and enter the following command

```bash
dmesg | tail
```
My result is something like:
```bash
[7887692.120663] usb 1-2: Product: STM32 STLink
[7887692.120669] usb 1-2: Manufacturer: STMicroelectronics
[7887692.120675] usb 1-2: SerialNumber: 0670FF485570854967105845
[7887692.148311] audit: type=1400 audit(1757366322.272:21348): apparmor="DENIED" operation="open" class="file" profile="snap.spotify.spotify" name="/sys/devices/pci0000:00/0000:00:14.0/usb1/1-2/descriptors" pid=2512520 comm="ThreadPoolForeg" requested_mask="r" denied_mask="r" fsuid=1000 ouid=0
[7887692.172919] cdc_acm 1-2:1.1: ttyACM0: USB ACM device
[7887692.172966] usbcore: registered new interface driver cdc_acm
```
The `ttyACM0` text indicates that the ST-LINK is located at `/dev/ttyACM0`. We will access the serial console through this device to view logs from our board.

```bash
sudo apt install -y picocom
sudo usermod -a -G dialout $USER

# Enter the following in a new terminal
picocom -b 115200 /dev/ttyACM0
```
After entering the last command above, power the board. You should see the boot process and a login prompt. I have something like:
```bash
...
[    3.033417] hub 1-0:1.0: 1 port detected
[    3.045219] ehci-platform 5800d000.usb: EHCI Host Controller
[    3.049526] ehci-platform 5800d000.usb: new USB bus registered, assigned bus number 2
[    3.059427] ehci-platform 5800d000.usb: irq 69, io mem 0x5800d000
[    3.080933] ehci-platform 5800d000.usb: USB 2.0 started, EHCI 1.00
[    3.087154] hub 2-0:1.0: USB hub found
[    3.089597] hub 2-0:1.0: 2 ports detected
[    3.106146] EXT4-fs (mmcblk0p5): mounting ext2 file system using the ext4 subsystem
[    3.118927] EXT4-fs (mmcblk0p5): warning: mounting unchecked fs, running e2fsck is recommended
[    3.129533] EXT4-fs (mmcblk0p5): mounted filesystem fb8ce85a-0558-4528-a5cb-8873aa3885b8 r/w without journal. Quota mode: disabled.
[    3.140264] VFS: Mounted root (ext2 filesystem) on device 179:5.
[    3.149824] devtmpfs: mounted
[    3.155829] Freeing unused kernel image (initmem) memory: 2048K
[    3.161056] Run /sbin/init as init process
[    3.345598] EXT4-fs (mmcblk0p5): re-mounted fb8ce85a-0558-4528-a5cb-8873aa3885b8 r/w. Quota mode: disabled.
[    3.361067] usb 2-1: new high-speed USB device number 2 using ehci-platform
Seeding 256 bits and crediting
Saving 256 bits of creditable seed for next boot
Starting syslogd: OK
Starting klogd: OK
[    3.533856] hub 2-1:1.0: USB hub found
Running sysctl: [    3.539363] hub 2-1:1.0: 4 ports detected
OK
Starting watchdog...
Set permissions on /dev/tee*: chown: /dev/teepriv0: No such file or directory
FAIL
Starting network: OK
Starting crond: OK

OP-TEE embedded distrib for stm32mp1-157A_DK1
buildroot login: 
```
This login prompt is good news!! It means our OP-TEE build and Linux Kernel are running successfully. Enter `root` in the login prompt and press `Enter`. If you enter `cd /`, you can see the minimal Linux userland. Now we can test OP-TEE.

## Testing OP-TEE
In Buildroot, OP-TEE consists of: `TEE supplicant` running in normal world, `libteec.so` (client library), and `xtest` (test TAs). Confirm these are present with:
```bash
which tee-supplicant # my result: /usr/sbin/tee-supplicant
which xtest # my result: /usr/bin/xtest
```
Start the supllicant daemon
```bash
tee-supplicant &
```
Run the [OP-TEE xtest](https://optee.readthedocs.io/en/latest/building/gits/optee_test.html), a TEE sanity tests suite for OP-TEE on Arm TrustZone. 
```bash
xtest
```
- A successful `xtest` run means you are ready to build your own OP-TEE trusted applications!

## Building a simple trusted application with OP-TEE
- TODO: find useful example online and include here.




## Troubleshooting
> The [OP-TEE Git repo]() has a list of open and closed issues which may help you.
- I opened a GitHub issue [here](https://github.com/OP-TEE/optee_os/issues/7521) on some issues I came across while testing OP-TEE on this board. The next article provides tips that solved my issue.
- [STM32 MPU OP-TEE configuration switches.](https://wiki.st.com/stm32mpu/wiki/OP-TEE_configuration_switches)
- [STM32 MPU How to build OP-TEE components](https://wiki.st.com/stm32mpu/wiki/How_to_build_OP-TEE_components)
  



## Other resources
- [Intro to Embedded Linux](https://www.digikey.ch/en/maker/projects/intro-to-embedded-linux-part-1-buildroot/a73a56de62444610a2187cd9e681c3f2)
- [Building OP-TEE for STM32MP1 boards](https://optee.readthedocs.io/en/latest/building/devices/stm32mp1.html)
- [STM32MP157D-DK1 databrief](https://www.st.com/resource/en/data_brief/stm32mp157d-dk1.pdf)
- [STM32MP157x-DKx - hardware description](https://wiki.st.com/stm32mpu/wiki/STM32MP157x-DKx_-_hardware_description)
- [STMicroelectronics software forum](https://community.st.com/t5/stm32-mpus-embedded-software-and/bd-p/mpu-embedded-software-forum)
- [STM32 MPU: How to develop an OP-TEE Trusted Application](https://wiki.st.com/stm32mpu/wiki/How_to_develop_an_OP-TEE_Trusted_Application)
- [STM32 MPU: OP-TEE overview](https://wiki.st.com/stm32mpu/wiki/STM32_MPU_OP-TEE_overview)
- [STM32 MPU: How to configure OP-TEE](https://wiki.st.com/stm32mpu/wiki/How_to_configure_OP-TEE)
- [STM32 MPU: OP-TEE configuration switches.](https://wiki.st.com/stm32mpu/wiki/OP-TEE_configuration_switches)
- [STM32 MPU: How to build OP-TEE components](https://wiki.st.com/stm32mpu/wiki/How_to_build_OP-TEE_components)
  

