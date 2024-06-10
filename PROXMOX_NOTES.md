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