# Awesome Raspberry Pi's Tutorials

Raspberry Pi's made simple! 

## Table of Contents

1. [Install Ubuntu on Raspberry Pi 3/4/5](#install-ubuntu-on-raspberry-pi-345)
2. [Install Kubernetes (MicroK8s) Server on Raspberry Pi 3/4/5](#install-kubernetes-microk8s-server-on-raspberry-pi-345)


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
3. Login using the username and password set in Advanced Options. If not set, the default is `ubuntu` for both.

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


### 1. Enabling cgroups

To run MicroK8s on ARM hardware (like Raspberry Pi), you must enable cgroups. Edit the boot parameters by running:

```bash
sudo vi /boot/firmware/cmdline.txt
```

**Note**: In some Raspberry Pi distributions, the boot parameters are located in `/boot/firmware/nobtcmd.txt`.

Add the following to the file:

```bash
cgroup_enable=memory cgroup_memory=1
```

### Installing Kernel Modules (Ubuntu 21.10+) ( Not necessary for Ubuntu 24.04+ )

If you are using Ubuntu 21.10 or later, install the extra kernel modules:

```bash
sudo apt install linux-modules-extra-raspi
```

### Installation

You can install MicroK8s using the snap package:

```bash
sudo snap install microk8s --classic
```

### Join the group

MicroK8s creates a group to enable seamless usage of commands that require admin privilege. To add your current user to the group and gain access to the .kube caching directory, run the following three commands:

```bash
sudo usermod -a -G microk8s $USER
mkdir -p ~/.kube
chmod 0700 ~/.kube
```

You will also need to re-enter the session for the group update to take place:

```bash
su - $USER
```

### Check the status

MicroK8s has a built-in command to display its status. During installation you can use the `--wait-ready` flag to wait for the Kubernetes services to initialize:

```bash
microk8s status --wait-ready
```

### Accessing Kubectl

MicroK8s bundles its own version of kubectl for accessing Kubernetes. Use it to run commands to monitor and control your Kubernetes. For example, to view your node:

```bash
microk8s kubectl get nodes
```

…or to see the running services:

```bash
microk8s kubectl get services
```

MicroK8s uses a namespaced kubectl command to prevent conflicts with any existing installs of kubectl. If you don’t have an existing install, it is easier to add an alias (append to `~/.bash_aliases`) like this:

```bash
alias kubectl='microk8s kubectl'
```

### Deploy an App

Kubernetes is meant for deploying apps and services. You can use the kubectl command to do that as with any Kubernetes. Try installing a demo app:

```bash
microk8s kubectl create deployment nginx --image=nginx
```

It may take a minute or two to install, but you can check the status:

```bash
microk8s kubectl get pods
```



