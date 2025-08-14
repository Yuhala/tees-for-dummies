## AMD secure encrypted virtualiation (SEV)
> As the name suggests, SEV is a TEE for creating confidential virtual machines (CVMs) on AMD-based processors. It uses AES to encrypt a CVM's private memory. The latter can only be decrypted within the CPU when the CVM is being executed.

## Setup
- To configure and enable TDX on your server, follow this [Readme from AMD](https://github.com/AMDESE/AMDSEV/blob/master/README.md). Here we will use the example setup provided for Ubuntu 18.04.

### Prepare the host OS
- First enable source repositories
```bash
sudo sed -i '/deb-src/s/^# //' /etc/apt/sources.list && sudo apt update
```
- Configure network with `sudo virsh net-start default`
Build and install components used for VM creation and management
```bash
git clone https://github.com/AMDESE/AMDSEV.git
cd distros/ubuntu-18.04
./build.sh # or sudo ./build.sh
```
### Prepare the VM image
Create an empty virtual disk image
```bash 
qemu-img create -f qcow2 ubuntu-18.04.qcow2 30G
```
Create a new copy of OVMF_VARS.fd. The OVMF_VARS.fd is a "template" used to emulate persistent NVRAM storage. Each VM needs a private, writable copy of VARS.fd.
```bash
sudo cp /usr/share/OVMF/OVMF_VARS.fd OVMF_VARS.fd
# cp /usr/local/share/qemu/OVMF_VARS.fd OVMF_VARS.fd
sudo ln -s /usr/share/OVMF/OVMF_CODE.fd /usr/local/share/qemu/OVMF_CODE.fd
```
Symlink qemu binary to `/usr/local/bin` in case it isn't there
```bash
sudo ln -s $(which qemu-system-x86_64) /usr/local/bin/qemu-system-x86_64
```
Install Ubuntu-18.04 guest
```bash
# launch-qemu.sh script is in the distros subfolder 
wget https://releases.ubuntu.com/bionic/ubuntu-18.04.6-live-server-amd64.iso
sudo ./launch-qemu.sh -hda ubuntu-18.04.qcow2 -cdrom ubuntu-18.04.6-live-server-amd64.iso
```
### Run the SEV guest
Launch the SEV guest VM with
```bash 
launch-qemu.sh -hda ubuntu-18.04.qcow2
```

## AMD SEV-SNP
> SEV-SNP is SEV with secure nested paging (SNP). It is an upgrade of SEV that adds integrity and replay protection to guest-private memory; this was absent in SEV.

### Host setup 
TODO


## Resources
- See the [SEV-SNP whitepaper](https://www.amd.com/content/dam/amd/en/documents/epyc-business-docs/white-papers/SEV-SNP-strengthening-vm-isolation-with-integrity-protection-and-more.pdf) for more information.