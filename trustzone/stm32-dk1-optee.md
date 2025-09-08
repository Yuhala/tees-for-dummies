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
mkdir -p optee-stm32mp1
cd optee-stm32mp1
repo init -u https://github.com/OP-TEE/manifest.git -m stm32mp1.xml 
repo sync 
```
4. **Build the toolchains**: Build the cross-compilation toolsets needed to build software for the target hardware architecture.
```bash
cd optee-stm32mp1/build
make -j2 toolchains
```
5. **Build the solution**: This is the core compilation step where all the downloaded source code components (OP-TEE OS, Linux kernel, Trusted Firmware-A, U-Boot, xtest, the root filesystem, etc.) are compiled together to create a complete, bootable software stack for your chosen platform. 
```bash
make -j `nproc` PLATFORM=stm32mp1-157A_DK1 all
```
This step takes some time. If you encounter build issues, you can pipe the build to a log file and check for errors. In this case, you should also avoid the `-j` flag for multi-threaded build so the log results are understandable. 

When the build completes, it generates an image file `sdcard.img` in the `../out/bin/` from build root path. The image is a GPT multipartition image you can copy to the target SD-card.

6. **Flash the device**: Now we flash the device with the built image. Insert the SD-card into your work PC and search for its path with `lsblk`; flash the device with the command below. The command `sgdisk -e /dev/sdX` converts the partition table on the device `/dev/sdX` from MBR (Master Boot Record) format to GPT (GUID Partition Table) format, while attempting to preserve the existing partition data.
```bash
sudo dd if=../out/bin/sdcard.img of=/dev/sdX conv=fdatasync status=progress
sudo sgdisk -e /dev/sdX
```
7. **Boot the board**: Insert the SD-card into the board and power it on.
- TODO: explain how to use the USB micro cable to access serial terminal.

```bash
dmesg | tail # example result is /dev/ttyACM0
sudo apt install -y picocom
sudo usermod -a -G dialout $USER

# Enter the following in a new terminal
picocom -b 115200 /dev/ttyACM0
```
After entering the last command above, power the board. You should see the boot process and a login prompt. 


## Testing a Trusted Application (TA) secured with TrustZone
- TODO















## Other resources
- [Intro to Embedded Linux](https://www.digikey.ch/en/maker/projects/intro-to-embedded-linux-part-1-buildroot/a73a56de62444610a2187cd9e681c3f2)
- [Building OP-TEE for STM32MP1 boards](https://optee.readthedocs.io/en/latest/building/devices/stm32mp1.html)
- [STM32MP157D-DK1 databrief](https://www.st.com/resource/en/data_brief/stm32mp157d-dk1.pdf)