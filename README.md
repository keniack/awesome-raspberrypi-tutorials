# Awesome Raspberry Pi's Tutorials

Raspberry Pi's made simple! 

## Table of Contents

1. [Install Ubuntu Server on Raspberry Pi 3/4/5](#install-ubuntu-server-on-raspberry-pi-345)
2. [Install Kubernetes (MicroK8s) on Raspberry Pi 3/4/5](#install-kubernetes-microk8s-on-raspberry-pi-345)
3. [Install Knative on Kubernetes (MicroK8s) on Raspberry Pi 3/4/5](#install-knative-on-kubernetesmicrok8s-on-raspberry-pi-345)
4. [Deploy WebAssembly (Wasm) Serverless Applications with Knative and Kubernetes (MicroK8s) on Raspberry Pi 3/4/5](#deploy-webassembly-wasm-serverless-applications-with-knative-and-kubernetesmicrok8s-on-raspberry-pi-345)


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

### 3. Boot Ubuntu Server

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


## Install Kubernetes (Microk8s) on Raspberry Pi 3/4/5


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

## Install Knative on Kubernetes(Microk8s) on Raspberry Pi 3/4/5

### 1. Prerequisites

Before installing Knative on MicroK8s, ensure you have the following:

1. [A Raspberry Pi 3, 4, or 5 with Ubuntu installed](#install-ubuntu-server-on-raspberry-pi-345).
2. [MicroK8s installed](#install-kubernetes-microk8s-on-raspberry-pi-345). 

### 2. Enable Community Add-ons

Knative is part of the MicroK8s community add-ons. To enable Knative, you must first enable the community add-ons repository:

```bash
microk8s enable community
```

---

### 3. Install Knative

After enabling the community repository, you can now install Knative using MicroK8s:

```bash
microk8s enable knative
```

This will install both the **Knative Serving** and **Knative Eventing** components to manage your serverless workloads.

---

### 4. Verify Installation

To verify that Knative has been successfully installed, you can run:

```bash
microk8s kubectl get pods -n knative-serving
microk8s kubectl get pods -n knative-eventing
```

Check that all pods are running and that Knative is ready to serve your serverless applications.

---

### 5. Deploy a Sample Knative Service

```bash
cat <<EOF | kubectl apply -f -
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: hello
spec:
  template:
    spec:
      containers:
      - image: csantanapr/helloworld-go:latest
        ports:
        - containerPort: 8080
        env:
        - name: TARGET
          value: "Knative"
EOF
```

This will deploy a simple Knative service that returns a "Hello Knative" message.

### 6. Verify Deployment 

```
kubectl get service.serving.knative.dev
```

### 7. Testing Deployed Service

```
kubectl get service.serving.knative.dev
```
#### 7.1 Get the IP address of the Ingress Gateway

First, you'll need to get the IP address of your Ingress:

```bash
microk8s kubectl get service kourier-internal -n knative-serving -o wide
```

Example:

```
microk8s kubectl get service kourier-internal -n knative-serving -o wide

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
kourier-internal   ClusterIP   10.152.183.42   <none>        80/TCP,443/TCP   15d   app=3scale-kourier-gateway
```

#### 7.2 Get the Service URL

Next, you need the URL of the Knative service. You can retrieve the service’s internal URL using:

```
microk8s kubectl get service.serving.knative.dev
```
You’ll see output like this:

```
NAME    URL                                        LATESTCREATED   LATESTREADY    READY   REASON
hello   http://hello.default.svc.cluster.local     hello-xyz       hello-xyz      True
```

#### 7.4 Call the Service

You can now curl the service by using the Cluster-IP address and full URL:

```bash
curl -v -H "Host: hello.default.svc.cluster.local" http://10.152.183.42 -d "Hello World!"
```

This will invoke the Knative service directly without requiring DNS.

### Additional Resources

- [Knative Documentation](https://knative.dev/docs/)
- [MicroK8s Add-ons](https://microk8s.io/docs/addons)

## Deploy WebAssembly (Wasm) Serverless Applications with Knative and Kubernetes(Microk8s) on Raspberry Pi 3/4/5

### 1. Prerequisites

Before installing Knative on MicroK8s, ensure you have the following:

1. [A Raspberry Pi 3, 4, or 5 with Ubuntu installed](#install-ubuntu-server-on-raspberry-pi-345).
2. [MicroK8s installed](#install-kubernetes-microk8s-on-raspberry-pi-345).
3. [Knative](#install-knative-on-kubernetesmicrok8s-on-raspberry-pi-345)


### 2. Enable kwasm

To enable WebAssembly support on MicroK8s, you first need to enable the kwasm addon. Run the following command:

```bash
microk8s enable kwasm
```

This will enable the required components to support WebAssembly applications in Kubernetes.

---

### 3. Deploy Wasm Runtime

Now that you have the WebAssembly runtime installed on the nodes via kwasm operator, you can deploy a runtime class for your Wasm applications. Use the following YAML to create the `RuntimeClass` for `wasmedge`:

```bash
cat <<EOF | microk8s kubectl apply -f -
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: wasmedge
handler: wasmedge
EOF
```

This will set up `wasmedge` as the runtime for running Wasm-based serverless applications on Kubernetes with MicroK8s. More runtime examples (spin,wasmtime,wasmer) in the file [examples/runtimeclass.yml](examples/runtimeclass.yml).

### 4. Remove Knative Serving Validation

Knative does not allow deploying resources with `runtimeClassName`, which is required to run WebAssembly workloads using `wasmedge`. To bypass this limitation, you need to delete the Knative Validating Webhook that enforces this restriction.

Check validations
```bash
kubectl get ValidatingWebhookConfiguration
```

Removing serving validation

```bash
kubectl delete ValidatingWebhookConfiguration validation.webhook.serving.knative.dev
```

### 5. Deploy a Wasmedge Function on Knative

```bash
cat <<EOF | kubectl apply -f -
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: wasm-http-server
  namespace: default
spec:
  template:
    metadata:
      annotations:
        module.wasm.image/variant: compat-smart
    spec:
      runtimeClassName: wasmedge
      containers:
      - name: funcb
        image: docker.io/keniack/wasm-http-server:latest
        ports:
        - containerPort: 8080
EOF
```

This will deploy a simple Knative service that returns a "Hello World" message.

### 6. Verify Deployment 

```
kubectl get service.serving.knative.dev
```

### 7. Testing Deployed Service

```
kubectl get service.serving.knative.dev
```
#### 7.1 Get the IP address of the Ingress Gateway

First, you'll need to get the IP address of your Ingress:

```bash
microk8s kubectl get service kourier-internal -n knative-serving -o wide
```

Example:

```
microk8s kubectl get service kourier-internal -n knative-serving -o wide

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
kourier-internal   ClusterIP   10.152.183.42   <none>        80/TCP,443/TCP   15d   app=3scale-kourier-gateway
```

#### 7.2 Get the Service URL

Next, you need the URL of the Knative service. You can retrieve the service’s internal URL using:

```
microk8s kubectl get service.serving.knative.dev
```
You’ll see output like this:

```
NAME    URL                                               LATESTCREATED          LATESTREADY    READY   REASON
hello   http://wasm-http-server.default.svc.cluster.local wasm-http-server-xyz   wasm-http-server-xyz      True
```

#### 7.3 Call the Service

You can now curl the service by using the Cluster-IP address and full URL:

```bash
curl -v -H "Host: wasm-http-server.default.svc.cluster.local" http://10.152.183.42 -d "Hello World!"
```

This will invoke the Knative service directly without requiring DNS.


