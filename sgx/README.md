## Intel software guard extensions (SGX)
> Intel SGX is a TEE technology which enables applications to create secure encrypted memory regions called **enclaves**. 
> Enclave memory is inaccessible to privileged software like the OS or hypervisor and can only be decrypted inside the CPU.

## System setup
To develop and run applications in an SGX TEE, you need to install three pieces of software
    1. The Intel SGX SDK
    2. The Intel SGX platform software (PSW)
    3. The Intel SGX driver
   
You can either build these sofware packages from source (See [Intel SGX GitHub](https://github.com/intel/linux-sgx)) or use prebuilt binaries. Given that we are dummies, we will go for the prebuilt binaries. 

You can find different [software releases] of these software [here](). For the meantime, we shall focus on the links called "Intel(R) SGX Installers ..." which contain the SDK, PSW, and driver. The "DCAP Installers" are used for handling attestation which we don't need yet. For example, to download the SDK, PSW, and driver for Ubuntu 24.04, you will use [this link](https://download.01.org/intel-sgx/sgx-linux/2.26/distro/ubuntu24.04-server/)

Install software packages required packages by SGX software.
```bash
sudo apt-get install build-essential ocaml automake autoconf libtool wget python3 libssl-dev dkms
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 1
```
1. **SGX driver installation**: Mainline kernel release 5.11 or higher includes an SGX in-kernel driver: `/dev/{sgx_enclave, sgx_provision}`. You can verify the presence of this with `ls -la /dev/sgx*`. If your kernel does not include the in-kernel driver, download and install the driver for your Ubuntu release. For example, for Ubuntu 24.04:
```bash
wget https://download.01.org/intel-sgx/sgx-linux/2.26/distro/ubuntu24.04-server/sgx_linux_x64_driver_1.41.bin #
chmod 777 sgx_linux_x64_driver_${version}.bin # make the installer executable
sudo ./sgx_linux_x64_driver_${version}.bin # run the installer
```

1. **SGX PSW installation**: The PSW is a runtime software stack to support the execution of SGX applications. For example, it handles SGX enclave creation, SGX context switches, attestation services, etc.
- Setup apt source list to point to prebuilt PSW packages on Intel's servers.
```bash
# For Ubuntu 24.04; see SGX installation guide for other OS versionss
echo 'deb [trusted=yes arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu noble main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
```
- Install packages
```bash 
sudo apt-get update
sudo apt-get install libsgx-epid libsgx-quote-ex libsgx-dcap-ql
```
1. **SGX SDK installation**: The SDK provides a framework for building SGX-based applications. Download and install the prebuilt package as follows.
```bash
wget https://download.01.org/intel-sgx/sgx-linux/2.26/distro/ubuntu24.04-server/sgx_linux_x64_sdk_2.26.100.0.bin # download SDK installer
chmod +x sgx_linux_x64_sdk_2.26.100.0.bin # make installer executable
sudo ./sgx_linux_x64_sdk_2.26.100.0.bin # run SDK installer
# Specify /opt/intel as install directory
```
For further instructions on how to install and setup these software components, see the [Intel SGX SW Installation Guide for Linux](https://download.01.org/intel-sgx/latest/dcap-latest/linux/docs/Intel_SGX_SW_Installation_Guide_for_Linux.pdf).


## Testing simple SGX applications
The SGX SDK provides sample applications in it's installation directory. If you installed the SDK in `/opt/intel`, the sample code folder should be located in `/opt/intel/sgxsdk/SampleCode`. Copy this folder to your working directory, e.g., somewhere in your home folder.
```bash
sudo cp -rf /opt/intel/sgxsdk/SampleCode .
sudo chown -R $USER:$USER SampleCode # fixes permission issues 
```
Build and run a simple SGX program like `SampleEnclave` to test your SGX installation.
```bash
cd SampleCode/SampleEnclave
make 
./app # or sudo ./app
```
The result "Sample Enclave successfully returned" indicates your SGX program ran successfully.

## Tweaking the sample enclave application
TODO: do an ecall and ocall and show that memory is encrypted by dumping enclave vs non-enclave memory and inspecting. 




For further information on writing Intel SGX software, see the [Intel SGX Developer Reference for Linux]().