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

vscode can use ssh

remote -ssh extension is needed

saving keys and changing configs in file path C:\Users\USERNAME\.ssh

```
Host alias
    HostName hostname
    User user
    IdentityFile [path_to_your_private_key] C:\\Users\\YourUsername\\.ssh\\id_ed25519 (C:\\path\\to\\your\\private_key) windows specific

```