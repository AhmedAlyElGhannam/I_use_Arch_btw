# Arch System ft. GNOME

## Introduction

This is a guide on how to install Arch manually for the fun of it. Then, GNOME will be installed as the desktop environment of choice. Additionally, I will add sections for all extensions/apps I use, so feel free to skip that. Make sure you use ethernet instead of wifi to avoid unnecessary obstacles.

## Prologue: Installation Medium Preparation (Ventoy)
  1. Download arch iso image from their official website.
  2. Either burn it to a flash drive using balena etcher **OR** use Ventoy instead for multi-OS boot from the same drive.
-- details will be added later


## Book One: Arch Installation

### Chapter One: Pre-pre-requisites


1. Check your internet connection using `ping`

  ```
  ping google.com
  ```

2. Enable network time synchronization

  ```
  timedatectl set-ntp true
  ```


### Chapter Two: Drive Partitioning & Mounting


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


### Chapter Three: Install Arch

Now, with your filesystem mounted, simply install Arch along with some essentials. A text editor is needed in the following steps. I chose to install `vim` but feel free to install whatever suits you.

  ```
  pacstrap /mnt base base-devel linux linux-firmware vim
  ```


### Chapter Four: Make an fstab File (This file contains all partitions Arch needs to load on boot)

  ```
  genfstab -U /mnt >> /mnt/etc/fstab
  ```
  The `-U` argument is added to tie the system with the actual drive ID instead of `sda` which might change if drives change.


### Chapter Five: Launch Arch and Setup NM & Grub

1. Launch Arch via the following command:
   
    ```
    arch-chroot /mnt /bin/bash
    ```
2. Use Arch's package manager `pacman` to install `networkmanager` and `grub`:

   ```
   pacman -S networkmanager grub
   ```

3. Configure `systemd` to start `networkmanager` on boot:

   ```
   systemctl enable NetworkManager
   ```

4. Install `grub` on your drive:

   ```
   grub-install /dev/sda
   ```

5. Generate `grub`'s boot config file:

   ```
   grub-mkconfig -o /boot/grub/grub.cfg
   ```
   **NOTE:** If you get a warning message saying: os-prober will not be executed to detect other bootable partitions, open the following file in vim `vim /etc/default/grub` and uncomment the line that says "GRUB_DISABLE_OS_PROPER=false".

### Chapter Six: Adding Finishing Touches

1. There must be a password for the root use. So, set it!

   ```
   passwd
   ```

2. To add locale in order to display/write in English language:

   ```
   #add locale
   vim /etc/locale.gen
   // uncomment "en_US.UTF-8" && "en_US ISO-8859-1"
   #generate locale
   locale-gen
   # define the used language
   vim /etc/locale.conf
   // add the following line:
   LANG=en-US.UTF-8
   ```

3. In order to change the hostname:

   ```
   vim /etc/hostname
   // write the name you want then save and exit
   ```

4. Set your country's time zone. In my case, I used Cairo's configuration. You can look for your country in the `zoneinfo` folder.

   ```
   ln -sf /usr/share/zoneinfo/Africa/Cairo /etc/localtime
   ```

5. The last step is to create a new user, add them to wheel group, set their password, and grant them sudo privilege (the ability to use sudo).

   ```
   # add user to wheel group
   useradd -mg wheel USERNAME
   # set user password
   passwd USERNAME
   # adjust group privilege in sudoers file
   vim /etc/sudoers
   // uncomment the line: %wheel ALL=(ALL:ALL) ALL
   // esc wq! enter
   ```


## Book Two: Installing GNOME (Desktop Environment)

1. Update your system and restart.

   ```
   sudo pacman -Syu
   sudo reboot
   ```

2. Install the window manager `Xorg`. Choose all the default options if you do not know what to choose.

   ```
   sudo pacman -S xorg xorg-server
   ```

3. Install GNOME. Choose all the default options if you do not know what to choose.

   ```
   sudo pacman -S gnome
   ```

4. Once GNOME is installed, start `gdm` service:

   ```
   sudo systemctl start gdm.service
   ```

5. Lastly, make sure to run the following command to run `gdm` on startup:

   ```
   sudo systemctl enable gdm.service
   ```


## Book Three: Drip is Unbreakable


 
