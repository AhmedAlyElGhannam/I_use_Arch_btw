# ArchSystemGuide_ft_GNOME

## Introduction

This is a guide on how to install Arch manually for the fun of it. Then, GNOME will be installed as the desktop environment of choice. Additionally, I will add sections for all extensions/apps I use, so feel free to skip that. Make sure you use ethernet instead of wifi to avoid unnecessary obstacles.

## Prologue: Installation Medium Preparation (Ventoy)
-- later


## Part One: Pre-pre-requisites


1. Check your internet connection using `ping`

  ```
  ping google.com
  ```

2. Enable network time synchronization

  ```
  timedatectl set-ntp true
  ```


## Part Two: Drive Partitioning & Mounting


1. Use `lsblk` to determine which disk to partition and format. In my case, since I disconnected all disks except the one I want to install Arch on, it is `sda`.


2. Use `cfdisk` to begin partitioning:

  ```
  cfdisk /dev/sda
  ```

3. Delete any partitions so that all that remains is free space. Now, create partitions with the following characteristics in the following order:
   1. 2G partition - primary (for `EFI`).
   1. 1G partition - primary (for `boot`---**MAKE SURE TO HIGHLIGHT IT AND PRESS `b` TO MAKE IT BOOTABLE**).
   1. 100G partition - primary (for `root`---I intend on dual booting so I only gave it 100GB to have the rest of the disk as `home` but if you do not intend to do that, just use the rest of the available disk space as `root`).
   1. partition using the remaining space - primary (for `home`---I used a 480GB SSD so I made `home` around 375GB).

4. Exit `cfdisk`. Then format the partitions created above. Make sure to format them all as `ext4` **EXCEPT** for `EFI` partition: it has to be formated as `FAT32` or GRUB will throw random errors at you.

   ```
   # efi
   mkfs.fat -F32 /dev/sda1
   # boot
   mkfs.ext4 /dev/sda2
   # root
   mkfs.ext4 /dev/sda3
   # home
   mkfs.ext4 /dev/sda4
   ```

5. Next step is to mount the partitions:

   ```
   # root
   mount /dev/sda3 /mnt
   # efi
   mkdir -p /mnt/boot/efi
   mount /dev/sda1 /mnt/boot/efi
   # boot
   mount /dev/sda2 /mnt/boot
   # home
   mkdir -p /mnt/home
   mount /dev/sda4 /mnt/home
   ```


## Part Three: Install Arch

Now, with your filesystem mounted, simply install Arch along with some essentials. A text editor is needed in the following steps. I chose to install `vim` but feel free to install whatever suits you.

  ```
  pacstrap /mnt base base-devel linux linux-firmware vim
  ```


## Part Four:
   -- tbc
   

 
