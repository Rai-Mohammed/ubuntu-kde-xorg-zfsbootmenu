# Ubuntu 25.10, KDE Plasma 6 DE, Xorg with ZFSBootMenu Installation Script
Bash script to install Ubuntu 25.10, KDE Plasma 6 DE, Xorg with ZFS on Root and ZFSBootMenu, the ZFSBootMenu page reference is : https://docs.zfsbootmenu.org/en/latest/guides/ubuntu/uefi.html

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
   root@ubuntu:~# bash

   # Check available network interfaces
   root@ubuntu:~# ip addr show

   # Step : Configure Netplan
   
   # edit the network interfaces file to insure internet and Lan connectivities (Case of two network interfaces)
   root@ubuntu:~# nano /etc/netplan/00_--------config.yaml
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

   root@ubuntu:~# chmod 600 /etc/netplan/00_--------config.yaml
   root@ubuntu:~# netplan generate
   root@ubuntu:~# netplan apply || true

   # Step : Define New Routing Tables
   root@ubuntu:~# mkdir -p /etc/iproute2
   root@ubuntu:~# touch /etc/iproute2/rt_tables
   root@ubuntu:~# nano /etc/iproute2/rt_tables
   1    table_net
   2    table_hoa

   # Step : Add Routes to the New Tables
   root@ubuntu:~# ip route add default via 10.0.2.2 dev enp0s3 table table_net
   root@ubuntu:~# ip route add default via 192.168.59.1 dev enp0s8 table table_hoa

   # Step : Add Policy Routing Rules
   root@ubuntu:~# ip rule add from 10.0.2.228/24 table tab
   root@ubuntu:~# ip rule add from 192.168.59.228/24 table table_hoa

   # Step : Verify Configuration
   ip route flush cache
   root@ubuntu:~# ip rule show
   root@ubuntu:~# ip route show table table_net
   root@ubuntu:~# ip route show table table_hoa

   # Do not test the connectivity by a ping, it doesn't work,
   # but you can update the system by "apt update && apt upgrade"

   # Test update APT
   root@ubuntu:~# apt update
   ```

2. **Downloading the script and editing it**
   
   Run the following to start the script

   ```bash   
   root@debian:~# apt install curl
   root@debian:~# curl -O https://raw.githubusercontent.com/Rai-Mohammed/ubuntu-kde-xorg-zfsbootmenu/main/ubuntu-kde-xorg-zfsbootmenu.sh

   # Make the necessary changes to the installation script
   root@debian:~# nano ubuntu-kde-xorg-zfsbootmenu.sh

   root@debian:~# chmod +x ubuntu-kde-xorg-zfsbootmenu.sh
   root@debian:~# ./ubuntu-gnome-wayland-zfsbootmenu.sh
   ```

