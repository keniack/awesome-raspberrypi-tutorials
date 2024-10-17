# Awesome Raspberry Pi's Tutorials

Raspberry Pi's made simple! 

## Table of Contents

1. [Install Ubuntu on Raspberry Pi 3/4/5](#install-ubuntu-on-raspberry-pi-345)
2. [Install Kubernetes(Microk8s) on Raspberry Pi 3/4/5](#install-Kubernetes-on-raspberry-pi-345)


## Install Ubuntu Server on Raspberry Pi 3/4/5

### 1. Prepare the SD Card

**Warning**: Following these steps will erase all existing content on the microSD card.

1. Insert the microSD card into your computer.
2. Install the Raspberry Pi Imager for your operating system:
   - [Raspberry Pi Imager for Ubuntu](https://www.raspberrypi.com/software/)
   - [Raspberry Pi Imager for Windows](https://www.raspberrypi.com/software/)
   - [Raspberry Pi Imager for macOS](https://www.raspberrypi.com/software/)
   - Or, on Ubuntu: `sudo snap install rpi-imager`
3. Start the Imager and open the "CHOOSE OS" menu.
4. Select "Other general-purpose OS" > "Ubuntu" and choose the latest **Ubuntu 24.04 LTS server** for 64-bit architectures.
5. Select the image, open the "Choose Storage" menu, and select your microSD card.
6. Before clicking 'Write', click the cog icon for advanced options.

### 2. Using Advanced Options

In the Advanced Options menu, you can:
- Set the hostname.
- Enable SSH for remote access.
- Configure Wi-Fi (SSID and password) for automatic connection.

After entering your details, click 'Save' and then 'Write' to flash the SD card. Eject the SD card and insert it into your Raspberry Pi.

### 5. Boot Ubuntu Server

1. Connect an HDMI screen and USB keyboard, then power on the Pi.
2. **Warning**: Wait for cloud-init to finish before logging in (typically less than 2 minutes).
3. Log in using the username and password set in the Advanced Options. If not set, the default is `ubuntu` for both.

#### Connect Remotely to Your Raspberry Pi

1. Find the Raspberry Pi’s IP address:
   - Check the router’s dashboard for connected devices.
   - Or, temporarily connect a monitor and run: `hostname -I`
2. Use an SSH client:
   - On Ubuntu/macOS (pre-installed) or Windows Terminal, run:
     ```
     ssh <username>@<Raspberry Pi’s IP address>
     ```
   - Or use the hostname set in Advanced Options.

3. Confirm the connection and log in.

For more details, visit the source: [Ubuntu Raspberry Pi Installation Tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi)


## Install Kubernetes (Microk8s) Server on Raspberry Pi 3/4/5
