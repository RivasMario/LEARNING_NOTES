# PROXMOX LAB NOTES

## HARDWARE

Mini ITX motherboard, baremetal fram case
Offbrand ZSUS B-75m Chinese clone motherboard bought off aliexpres ~24$
LGA 1150 cpu, 8gb ram currently (upgrading to 16gb) ICOD 07JUN2024
NVME m.2 500gb SSG
2x 250GB SSDs
1x 1TB SSD
1x 6tb HDD
AMD Graphics card (XXXXX)

## INSTALLATION

Flashed proxmox 8.2 onto m.2 ssd
connected to network, got .15ip 

port 8006 for app portal

## Auth

redoing my steps to auth using azure entraid

## DOMAIN

bought domain off domain.com , need to buy one specifically for homelab to connect to. Have one for resume site already



## KUBERNETES

add optional feature for OpehSSh on windows

powershell 

ssh-keygen -t ed25519 -C "kubernetes-vm-key-[VM_NAME]-[CLUSTER_NAME]"

ssh-keygen -t ed25519 -C "UBUNTU-VM-KEY-KUBERNETES"

vscode can use ssh

remote -ssh extension is needed

saving keys and changing configs in file path C:\Users\USERNAME\.ssh

```
Host alias
    HostName hostname
    User user
    IdentityFile [path_to_your_private_key] C:\\Users\\YourUsername\\.ssh\\id_ed25519 (C:\\path\\to\\your\\private_key) windows specific

```

### Create a no disk VM

do not select any media, yes qemu agent check box

set ip to dhcp, set drive imported to active, set it to boot first, set it to turn on each time.

set cloud init for each time, and an ssh key, ssd emulation

https://cloud-images.ubuntu.com/minimal/releases/jammy/release/

https://codingpackets.com/blog/proxmox-import-and-use-cloud-images/

has to be .img file

wget https://cloud-images.ubuntu.com/minimal/releases/jammy/release/ubuntu-22.04-minimal-cloudimg-amd64.img

qm set <VM ID> --serial0 socket --vga serial0

mv ubuntu-22.04-minimal-cloudimg-amd64.img ubuntu-22.04.qcow2

qemu-img resize ubuntu-22.04.qcow2 32G



apt install libguestfs-tools -y

virt-customize -a ubuntu-22.04.qcow2 --install qemu-guest-agent,ncat,net-tools,bash-completion

virt-customize -a ubuntu-22.04.qcow2  --install "qemu-guest-agent"

virt-customize -a ubuntu-22.04.qcow2  --truncate "/etc/machine-id"

qm importdisk 900 ubuntu-22.04.qcow2 local-lvm

then add drive to the vm in ui


ssh -i "C:\Users\Mario\.ssh\UBUNTU_VM_KEY_KUBERNETES" ubuntuuser@192.168.0.25 -v

sudo apt install containerd

ssh ubuntuuser@192.168.0.25 -i "C:\Users\Mario\.ssh\UBUNTU_VM_KEY_KUBERNETES"

ssh ubuntuuser@192.168.0.26 -i "C:\Users\Mario\.ssh\UBUNTU_VM_KEY_KUBERNETES"

Installing containerd

This Kubernetes cluster will utilize the containerd runtime. To set that up, we’ll first need to install the required package:

sudo apt install containerd

Create the initial configuration:

sudo mkdir /etc/containerd

containerd config default | sudo tee /etc/containerd/config.toml

For this cluster to work properly, we’ll need to enable SystemdCgroup within the configuration. To do that, we’ll need to edit the config we’ve just created:

sudo nano /etc/containerd/config.toml

Within that file, find the following line of text:

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]

Underneath that, find the SystemdCgroup option and change it to true, which should look like this:

SystemdCgroup = true

Disable swap

Kubernetes requires swap to be disabled. To turn off swap, we can run the following command:

free -m

sudo swapoff -a

Next, edit the /etc/fstab file and comment out the line that corresponds to swap (if present). This will help ensure swap doesn’t end up getting turned back on after reboot.

Enable bridging

To enable bridging, we only need to edit one config file:

sudo nano /etc/sysctl.conf

Within that file, look for the following line:

#net.ipv4.ip_forward=1

Uncomment that line by removing the # symbol in front of it, which should make it look like this:

net.ipv4.ip_forward=1

Enable br_netfilter

The next step is to enable br_netfilter by editing yet another config file:

sudo nano /etc/modules-load.d/k8s.conf

Add the following to that file (the file should actually be empty at first):

br_netfilter

Reboot your servers

Reboot each of your instances to ensure all of our changes so far are in place:

sudo reboot

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

Installing Kubernetes

The next step is to install the packages that are required for Kubernetes. First, we’ll add the required GPG key:

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://
packages.cloud.google.com/apt/doc/apt-key.gpg

After that, install the packages that are required for Kubernetes:

sudo apt install kubeadm kubectl kubelet

Controller node only: Initialize our Kubernetes cluster

So long as you have everything complete so far, you can initialize the Kubernetes cluster now. Be sure to customize the first IP address shown here (not the second) and also change the name to match the name of your controller.

sudo kubeadm init --control-plane-endpoint=172.16.250.216 --node-name
controller --pod-network-cidr=10.244.0.0/16

After the initialization finishes, you should see at least four commands printed within the output.
Setting up our user account to manage the cluster

Three commands will be shown in the output from the previous command, and these commands will give our user account access to manage our cluster. Here are those related commands to save you from having to search the output for them:

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

suod chown $(id -u):$(id -g) $HOME/.kube/config

Those commands should allow you to manage the cluster, without needing to use the root account to do so.

Install an Overlay Network

The following command will install the Flannel overlay network (an overlay network is required for this to function).

kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/
Documentation/kube-flannel.yml

Adding Nodes

The join command, which you will receive from the output once you initialize the cluster, can be ran on your node instances now to get them joined to the cluster. The following command will help you monitor which nodes have been added to the controller (it can take several minutes for them to appear):

kubectl get nodes

If for some reason the join command has expired, the following command will provide you with a new one:

kubeadm token create --print-join-command

kubeadm token create --print-join-command


sudo apt get update

sudo apt install kubeadm kubectl kubelet