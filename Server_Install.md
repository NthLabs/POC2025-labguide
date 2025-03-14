Install Ubuntu 22.04

Configure Network Bridge

```
$ sudo vi /etc/netplan/75-NthPOC.yaml
```
```
network:
    bonds:
        bond0:
            interfaces:
            - ens14f0np0
            - ens14f1np1
            parameters:
                mode: active-backup
    bridges:
        br0:
            interfaces:
            - bond0
            addresses:
            - 10.106.10.25/20
            nameservers:
                addresses:
                - 10.106.10.5
                search:
                - nvision.nthlabs.com
            routes:
            -   to: default
                via: 10.106.1.1
    ethernets:
        ens14f0np0: {}
        ens14f1np1: {}
    version: 2
```
Apply Networking Configuration

```
$ sudo apt update
$ sudo apt upgrade -y 

## disable cloud-init
$ sudo touch /etc/cloud/cloud-init.disabled

## remove cloud-init generated configs
$ sudo rm /etc/netplan/50-cloud-init.yaml
$ sudo reboot now
```

Install Management Component Pack

```
$ wget https://downloads.linux.hpe.com/SDR/repo/mcp/ubuntu/pool/non-free/amsd_3.6.0-1867-ubuntu22_amd64.deb
$ sudo apt install ./amsd_3.6.0-1867-ubuntu22_amd64.deb
```

Create pocadmin user

```
$ sudo useradd pocadmin -s /bin/bash -g sudo -m
$ sudo passwd pocadmin 
<<<Enter Password>>>
```

Create VM Storage 
Example given is for a 1.7TB mirror

```
$ sudo lvmdiskscan -l
$ sudo pvcreate /dev/sda /dev/sdb
$ sudo vgcreate data_vg /dev/sda /dev/sdb

$ sudo lvcreate -n data_vol1 -l 100%FREE -R 2 -m1 data_vg
```

Additional Storage Commands for reference only

```
## Remove:
$ sudo lvremove data_vg/data_vol1

## Display RAID Status
$ sudo lvs -a -o name,copy_percent,devices data_vg
```

Format & Mount Storage

```
$ sudo mkfs -t ext4 /dev/data_vg/data_vol1
$ sudo mkdir /VMs
$ sudo chown pocadmin /VMs
$ sudo mount -t ext4 /dev/data_vg/data_vol1 /VMs
```

Auto-mount storage at boot

```
$ sudo vi /etc/fstab
```
```
/dev/data_vg/data_vol1  /VMs    ext4    defaults        0       0
```
Disable auto-updates

```
$ sudo vi /etc/apt/apt.conf.d/20auto-upgrades
```
```
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
```

Configure NTP

```
$ sudo timedatectl set-timezone America/Los_Angeles
$ sudo apt install ntp -y
$ sudo ntpq -p

$ sudo reboot now
```

---

Login as pocadmin

Verify Changes

```
$ df -m  # Checking for disk mounts
$ date   # Checking time config
$ ip a   # Checking network config
```

Check Virtualization Support

```
$ sudo apt update
$ egrep -c '(vmx|svm)' /proc/cpuinfo
$ sudo apt install cpu-checker -y
$ sudo kvm-ok
```

Install KVM

```
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils -y
sudo adduser pocadmin libvirt
sudo adduser pocadmin kvm
sudo adduser labadmin libvirt
sudo adduser labadmin kvm

## Check Status of KVM
sudo systemctl status libvirtd
sudo virsh list --all

## Install Manager
sudo apt install virt-manager -y
```
