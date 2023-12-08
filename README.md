# Arch Linux Install Tutorial Document
This tutorial covers the steps I took while installing Arch Linux, as well as some problems and the solutions that were taken to fix them. This guide is meant for students at the University of Tulsa but can be a general guide aside from some specific sections.
## Section 1: Download Arch Linux
1. Go to the download page provided in the Arch Linux Wiki: [https://archlinux.org/download/](https://archlinux.org/download/).
2. Scroll down to the United States section and choose any of the links (mit.edu was used in this example).
3. Click on `archlinux-2023.11.01-x86_64.iso` and wait for it to download fully.
4. Make sure to verify the signature of the iso. This can be done with `pacman-key -v archlinux-version-x86_64.iso.sig`
## Section 2: Create the Arch Linux VM
1. VMWare Workstation should be downloaded and installed first.
2. Open up VMWare Workstation and click on the `Create a New Virtual Machine` option in the home screen.
3. Choose the Custom VM Option.
4. Make sure to check in the `Choose the Virtual Machine Hardware Compatibility` screen that the VM hardware is compatible.
5. In the `Installer disc image file` section, insert the Arch Linux iso you downloaded.
6. Choose `Other Linux 5.x kernel 64-bit` in the `Select a Guest Operating System` section.
7. Name the Virtual Machine. (This VM was named "Arch Linux" for simplicity).
8. Set the number of processors to 1 and the number of cores per processor to 2 in `Processor Configuration`.
9. Set the memory storage of the VM at 2 GB.
10. Use the default for the rest of the settings.
After that, in the Library section, right click on the Arch Linux VM and click on `Open VM Directory`.
Look for the file named `Arch Linux.vmx`, and open it in a word processor like Notepad. Underneath the line `encoding = "windows-1252"`, create a newline and type in `firmware = "efi"`, and save the file.
## Section 3: Modify Arch Linux
1. Open the Arch Linux VM.
2. Verify the boot mode using `cat /sys/firmware/efi/fw_platform_size`.
### Connect to the Internet:
1. Make sure that the VM has an internet connection by running `ip link`.
### Time and Language:
1. Check the system clock and make sure it is accurate by typing in `timedatectl`.
### Partitioning the Disks:
1. We first have to modify the parition table by typing in `fdisk /dev/sda`.
2. Next, type `n`.  This creates a new parition. Two paritions are needed for the VM: a root directory parition and an EFI system partition, since we are using EFI for the VM. 
3. For the first parition (sda1), type in `p` for primary, leave the parition number default, type in `2048` for the first sector and `+500M` for the last sector.
4. For the second parition (sda2), use the default settings for each option.
### Formatting the Partitions:
1. To format the EFI system parition, sda1, type in `mkfs.fat -F 32 /dev/sda1`.
2. To format the root directory partition, sda2, type in `mkfs.ext4 /dev/sda2`.
3. Make sure to check if the formatting is correct afterwards by typing in `lsblk -f`.
### Mounting the File Systems:
1. To mount sda1, a FAT32 file, type in `mount --mkdir /dev/sda1 /mnt/boot`.
2. To mount sda2, an Ext4 file, type in `mount /dev/sda2 /mnt`.
## Section 4: The Installation Process
### Installing Packages:
1. Type in `pacstrap -K /mnt base linux linux-firmware nano grub dhcpcd efibootmgr`. This will install both the basic linux firmware and the nano, grub, dhcpcd and efibootmgr commands which we will need later on.
### Configuring the System:
1. First, generate an fstab file by typing in `genfstab -U /mnt >> /mnt/etc/fstab`.
2. Next, change root into the new system by typing in `arch-chroot /mnt`.
3. To set up the timezone, first type in `ln -sf /usr/share/zoneInfo/America/Chicago /etc/localtime`. (Change depending on what timezone you live in.)
4. Next, type in `hwclock --systohc` to set up the assumption that the hardware clock is set to UTC.
### Localizing the System:
1. First, type in `nano /etc/locale.gen`, scroll down and uncomment `en_US.UTF-8 UTF-8` by erasing the "#". Save by pressing ctrl-x, then y, then ENTER.
2. Next, generate the locales by typing `locale-gen`. `US.UTF-8` should display when the command runs.
3. Lastly, type in `nano /etc/locale.conf` and enter in `LANG=en_US.UTF-8`. Save by pressing ctrl-x, then y, then ENTER.
### Network Configuration:
1. Firstly, create a hostname file by typing `touch /etc/hostname`. 
2. Next, type in `nano /etc/hostname` and enter in a hostname of your choice (example: noahrosenbloom). Save with ctrl-x, y, then ENTER.
3. After that, type in `nano /etc/hosts`, and enter in `127.0.0.1 localhost ::1 localhost 127.0.1.1 username` (username being similar to the hostname).
### Initramfs:
1. This is not a required step, but it is a step provided on the Arch Linux wiki installation guide and it is recommended.
2. Type in `mkinitcpio -p`.
### The Bootloader:
1. Type in `pacman -S man-pages texinfo sudo man-db sof-firmware dosfstools`. Add on `amd-ucode` or `intel-ucode` to the end depending on if you have an AMD or an Intel processor respectively. This will download more packages that we will need to install the bootloader.
2. Next we need to edit the packages. Type in the following: `sudo pacman -Syu`, then `sudo pacman -S wpa_supplicant wireless_tools networkmanager`, then `sudo pacman -S nm-connection-editor network-manager-applet`, then `sudo systemctl enable NetworkManager.service`, then `sudo systemctl enable wpa_supplicant.service`.
### Change the Password:
1. Type in `passwd` and then enter in the password you want to use and verify it by re-typing it.
### Installing the Bootloader:
1. First type `grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB` then type `grub-mkconfig -o /boot/grub/grub.cfg`.
2. Next type `mount /dev/sda1 /mnt` to mount the boot and then lastly type `grub-install --efi-directory=/dev/sda1`.
3. Type `reboot` after all that to reset the VM.
Problem: grub wouldn't install properly initially, had to unmount and remount the partitions before it worked.
## Section 4: Post-Installation
### Create and Sudo Users:
1. We need to add a couple users as part of the assignment. Type `sudo useradd -m boah' and then `sudo useradd -m codi` to add the users.
2. Next, type `sudo passwd noah` to edit the password for the "noah" user. Enter the password and verify it.
3. Then, type `sudo passwd codi` and type in the password `GraceHopper1906`.
4. We need to add the users to a group to give them sudo permission. Type `sudo groupadd sudo`, and then type `sudo usermod -a -G sudo codi` and then `sudo usermod -a -G sudo noah`.
5. You can check the password files with `less /etc/passwd`.
6. Next, call the command `EDITOR=/usr/bin/nano visudo`.
7. Finally, scroll down until you see `root ALL=(ALL:ALL) ALL` and type `noah ALL=(ALL:ALL) ALL` and then `codi ALL=(ALL:ALL) ALL`.
### Check for Updates:
1. Make sure to check for any updates with `pacman -Syu`.
### Install the Desktop Environment:
1. For this example, the desktop environment used was GNOME. To install it, type `pacman -S gnome` to install GNOME.
2. Type `systemctl start gdm.service` afterwards to allow GNOME to run on boot.
3. Type `reboot` to reset the system again.
### Changing the Shell:
Finding and opening the Console application in the GNOME interface helps with these steps.
1. Since we are going to be using zsh as the alternate shell in this case, we need to install it. Type `sudo pacman -S zsh`.
2. Then, call `chsh -s /usr/bin/zsh` to swap the shell to zsh
### Installing SSH:
1. First type `pacman -S openssh` to install openssh.
2. Check if it is active afterwards or not with `systemctl status sshd`. If it is not active, then proceed to type `systemctl start sshd`.
### Adding Color and Aliases to the Shell:
1. First, create the zsh config file with `touch ~/.zshrc`. Then type `nano ~/.zshrc` to edit the config file.
2. Type in `autoload -U colors && colors` first, then underneath it type `PS1="%{$fg[blue]%}%n%{$reset_color%}@%{$fg[green]%}%m %{$fg[yellow]%}%~ %{$reset_color%}%% "`
3. Finally, we want to add some aliases. Below the previous code, enter these lines, each underneath each other: `alias clr='clear'`, then `alias rb='sudo reboot'`, then `alias gh='history | grep'`, then finally `alias c='clear'`.
4. Save your changes with CTRL-X, then Y, then ENTER.
# Conclusion:
That concludes the tutorial. If you've made it this far, then everything has worked. Enjoy.
