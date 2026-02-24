# Ubuntu 25.10, Gnome DE, Wayland with ZFSBootMenu Installation Script
Bash script to install Ubuntu 25.10, Gnome DE, Wayland with ZFS on Root and ZFSBootMenu, the ZFSBootMenu page reference is : https://docs.zfsbootmenu.org/en/latest/guides/ubuntu/uefi.html

## Prerequisites

- A live installation environment (e.g., Ubuntu Server Live)
   1. **Remarque :**
      ```bash
      The first task is to download the latest Name: ubuntu-25.10-live-server-amd64.iso,
      this necessary to don''t have a miss matches packages between the live iso image and the
      "apt update && apt upgrade" instruction, causing the system to not be able loading the ZFS modules
      ```
- A disk available for partitioning and installation (existing data will be erased)
- Network connection for downloading packages and files

## Usage

1. **Configure Ubuntu Networking**
   
   Boot into your live environment, begin the install then abroat it, open the terminal, run the following

   ```bash
   # Switch to a root shell
   user@ubuntu:~$ sudo -i
   root@debian:~# bash

   # Check available network interfaces
   root@debian:~# ip addr show

   # edit the network interfaces file to insure internet and Lan connectivities (Case of two network interfaces)
   root@debian:~# nano /etc/netplan/00_--------config.yaml
  network:
    version: 2
    renderer: networkd
    ethernets:
        # VirtualBox Nat Adapter - For internet connectivity
        enp0s3:
            dhcp4: false
            accept-ra: true
            addresses: [10.0.2.228/24]
            routes:
               - to: default
                 via: 10.0.2.2
            nameservers:
                addresses: [8.8.4.4]
        # VirtualBox Host Only Adapter - For Lan connectivity
        enp0s8:
            dhcp4: false
            accept-ra: true
            addresses: [192.168.59.228/24]
            nameservers:
                addresses: [8.8.4.4]


   #----------------------------------
   root@debian:~# systemctl restart networking.service
   # Do not test the connectivity by a ping, it doesn't work,
   # but you can update the system by "apt update && apt upgrade"

   # Configure and update APT
   root@debian:~# nano /etc/apt/sources.list
   deb http://deb.debian.org/debian/ trixie main non-free non-free-firmware contrib
   deb-src http://deb.debian.org/debian/ trixie main non-free non-free-firmware contrib

   root@debian:~# apt update && apt upgrade
   ```

2. **Downloading the script and editing it**
   
   Run the following to start the script

   ```bash   
   root@debian:~# apt install curl
   root@debian:~# curl -O https://raw.githubusercontent.com/Rai-Mohammed/debian13-kde6-xorg-zfsbootmenu/main/debian13-kde6-xorg-zfsbootmenu.sh

   # Make the necessary changes to the installation script
   root@debian:~# nano debian13-kde6-xorg-zfsbootmenu.sh

   root@debian:~# chmod +x debian13-kde6-xorg-zfsbootmenu.sh
   root@debian:~# ./debian13-kde6-xorg-zfsbootmenu.sh
   ```

