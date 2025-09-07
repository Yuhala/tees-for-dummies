## VisionFive 2
> The VisionFive 2 development board is a RISC-V based computer from StarFive with an integrated 3D GPU. The VisionFive 2 has a fully open-source with open-source software and hardware design. It is powered by the StarFive JH7110 SoC which features:
>- CPU: Quad-core 64-bit RISC-V ([SiFive U74, RV64GC](https://starfivetech.com/uploads/u74_core_complex_manual_21G1.pdf)) with 2MB L2 cache and a monitor core (low-power auxiliary CPU core used for system management and control tasks, e.g., power management, boot control, etc), supports RV64GC ISA, and runs at up to 1.5GHz.
>- GPU: Imagination BXE-4-32 MC1 at up to 600MHz (The VisionFive  1 has no GPU).
>- Memory: 2,4,8 GB LPDDR4 DRAM (mine is 8GB)
>- 40-pin GPIO header supporting I2C, I2S, PWM, UART amongst others. 


## Setting up the VisionFive 2
Here we show how to install a Linux OS (Debian) on the VisionFive 2 and run a simple application. The setup instructions are based on [this guide](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf).
- Download the latest Debian image from [here](https://debian.starfivetech.com/) (e.g., 202409/sd folder in this case). We choose the SD image from OneDrive. Extract the downloaded image with `bzip2`.
```bash
bzip2 -dk starfive-jh7110-202409-SD-minimal-desktop-wayland.img.bz2 
```
- [Install BalenaEtcher](https://etcher.balena.io/) and open it.
- Connect a microSD card to your PC and use BalenaEtcher to flash the above image to the SD card: `Flash from file` --> `Select target card` --> `Flash!`.[^1] Once done insert the SD card into the board. 
<!--Setup the boot mode by toggling the [RGPIO buttons](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf) (page 39) accordingly. We use the SD card in our case which corresponds to `SDIO 3.0 mode`: `RGPIO_1 = L(0)` and `RGPIO_0 = H(1)` (use a small pin to toggle the buttons).-->
- Connect a display (e.g., HDMI), keyboard, and mouse to the board (we will configure SSH access subsequently), and power on the board.
- Once you have the login interface, enter the default login credentials: `Username: user, Password: starfive`. Configure SSH in the terminal as follows.
```bash
# Verify status
sudo systemctl status ssh

# Enale root SSH login
echo 'PermitRootLogin=yes' | sudo tee -a /etc/ssh/sshd_config
sudo systemctl restart sshd
```

- To connect to the VisionFive 2 via SSH, connect an ethernet cable to the ethernet port or use the WiFi dongle provided to connect to your local WiFi. You can then obtain the board's IP in the terminal with `hostname -I`.
- Alternatively, you can use a tool like `arp-scan` on a separate computer to obtain your board's IP address.
```bash
sudo apt install arp-scan
sudo arp-scan --localnet
```
Results of the scan will give you something like:
```bash
192.168.1.132   3C:97:0E:12:34:56  StarFive Technology Co., Ltd.
...
```
- You can then SSH into the VisionFive 2 using `ssh user@192.168.1.132` or `ssh root@192.168.1.132` (for root). 
- Consult the [VisionFive 2 quick start guide](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf) for further setup details.



## Testing PMP on the VisionFive 2















## Resources
- [Official VisionFive 2 documentation](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf)
- [VisionFive 2 datasheet](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_Datasheet.pdf)
- [YouTube video by ExplainingComputers on the VisionFive 2](https://www.youtube.com/watch?v=ykKnc86UtXg)



[^1]: The VisionFive SoC provides two-switch RGPIO header which determines which storage device will be used to load the OS image.[See RGPIO configuration here](https://doc-en.rvspace.org/VisionFive2/PDF/VisionFive2_QSG.pdf) (page 39)