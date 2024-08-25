# RTFM
A lot of the steps for this are found in the goddamn manual. Use your search skills and actually read how to set it up Bailey.

**Load the US Keymap** 

```zsh
loadkeys us
```

**Verify the bootmode**

```zsh
cat /sys/firmware/efi/fw_platform_size
```

This should return 64.

**Set the timezone**

```zsh
timedatectl set-timezone America/Los_Angeles
```

**Fill drives with Random Data**

This is actually pretty important for the security of our encrypted drives. 
It increases the hash strength when encrypting. Use `lsblk` to find the name of the drive you wish to fill
with random data. BE CAREFUL.

```zsh
dd if=/dev/urandom of=/dev/nvme0n1 status=progress bs=4096;
dd if=/dev/urandom of=/dev/sda status=progress bs=4096
```

**Create The Partitions for Each Drive**

For this I use gdisk as it works well with NVMe drives and auto spaces the sectors preventing corruption. The swap partition should be the amount of RAM you have in the system plus 2GB.

_nvme0n1_

|Partition|First Sector|Last Sector|Code|Type|
|---------|------------|-----------|----|----|
|1|default|+512M|ef00|EFI System Partition|
|2|default|+4G|ef02|BIOS Boot Partition|
|3|default|+66G|8200|Linux Swap|
|4|default|+256G|8309|Linux LUKS|

_sda_

|Partition|First Sector|Last Sector|Code|Type|
|---------|------------|-----------|----|----|
|1|default|+500G|8302|Linux /home|

## Encrypting The Disks

**Load Encryption Modules**

```zsh
modprobe dm-crypt;
modprobe dm-mod;
```

**Encrypt the LVM and Home Partitions**

```zsh
cryptsetup luksFormat -v -s 512 -h sha512 /dev/nvme0n1p4;
cryptsetup luksFormat -v -s 512 -h sha512 /dev/sda1
```

**Mount the drives**

```zsh
cryptsetup open /dev/nvme0n1p4 luks_lvm;
cryptsetup open /dev/sda1 arch-home
```

## Volume Setup

**Create the volumes and volume groups**

```zsh
pvcreate /dev/mapper/luks_lvm;
vgcreate arch /dev/mapper/luks_lvm
lvcreate -n root -l +100%FREE arch
```
## Formatting Filesystems

**FAT32 on EFI**

```zsh
mkfs.fat -F32 /dev/nvme0n1p1
```

**EXT4 on BIOS Boot Partition**

```zsh
mkfs.ext4 /dev/nvme0n1p2
```

**Swap on SWAP**

```zsh
mkswap /dev/nvme0n1p3
```

**BTRFS on Root**

```zsh
mkfs.btrfs -L root /dev/mapper/arch-root
```

**BTRFS on HOME**

```zsh
mkfs.btrfs -L home /dev/mapper/arch-home
```

## Mounting

**Mount & Active Swap**

```zsh
swapon /dev/nvme0np3
swapon -a
```

**Root**

```zsh
mount /dev/mapper/arch-root /mnt
```

**Creating Home & Boot Folders**

```zsh
mkdir -p /mnt/{home,boot}
```

**Mounting the Boot Partition**

```zsh
mount /dev/nvme0n1p2 /mnt/boot
```

**Mounting The Home Partition**

```zsh
mount /dev/mapper/arch-home /mnt/home
```

**Create EFI Directory**

```zsh
mkdir /mnt/boot/efi
```

**Mount EFI Directory**

```zsh
mount /dev/nvme0n1p1 /mnt/boot/efi
```

## Installing Arch

```zsh
pacstrap -K /mnt base base-devel linux linux-firmware
```

**Load the File Table**

```zsh
genfstab -U -p /mnt > /mnt/etc/fstab
```

**Chroot Into the Installation**

```zsh
arch-chroot /mnt /bin/bash
```
## Configuring

**Install Neovim**

```zsh
pacman -S neovim
```

** Edit mkinitcpio.conf and add encrypt and lvm2 into the Hooks()***

```zsh
nvim /etc/mkinitcpio.conf;
```

EX:

```zsh
HOOKS=(... block encrypt lvm2 filesystems fsck)
```

**Install GRUB and EFIBootMgr**

```zsh
pacman -S grub efibootmgr
```

**Setup GRUB on EFI Partition**

```zsh
grub-install --efi-directory=/boot/efi
```

**Get UUID of LVM Partition**

```zsh
blkid /dev/nvme0n1p4
```

Copy to clipboard and add to /etc/default/grub and add the following parameters

```zsh
root=/dev/mapper/arch-root cryptdevice=UUID=<uuid>:luks_lvm
```

### Keyfiles

```zsh
mkdir /secure
```

**Create Keyfiles**

```zsh
dd if=/dev/random of=/secure/root_keyfile.bin bs=512 count=8;
dd if=/dev/random of=/secure/home_keyfile.bin bs=512 count=8
```

**Change permissions of secure**

```zsh
chmod 000 /secure/*
```

**Add Keys to Partitions**

```zsh
cryptsetup luksAddKey /dev/nvme0n1p4 /secure/root_keyfile.bin;
cryptsetup luksAddKey /dev/sda1 /secure/home_keyfile.bin
```

Edit mkinitcpio.conf again and add the root keyfile to FILES

```zsh
FILES=(/secure/root_keyfile.bin)
```

**Setup Home Partition Crypttab**

```zsh
blkid /dev/sda1
```

```zsh
nvim /etc/crypttab
```

```zsh
arch-home      UUID=<uuid>      /secure/home_keyfile.bin
```

```zsh
mkinitcpio -p linux
```

## Regenerate GRUB Configs

```zsh
grub-mkconfig -o /boot/grub/grub.cfg
grub-mkconfig -o /boot/efi/EFI/arch/grub.cfg
```

## System Configuration

**Timezone**

```zsh
ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
```

**NTP**

```zsh
nvim /etc/systemd/timesyncd.conf
```

Add NTP Servers and replace default time block

```zsh
[Time]
NTP=0.arch.pool.ntp.org 1.arch.pool.ntp.org 2.arch.pool.ntp.org 3.arch.pool.ntp.org 
FallbackNTP=0.pool.ntp.org 1.pool.ntp.org
```

**Enable Timesyncd**

```zsh
systemctl enable systemd-timesyncd.service
```

## Locale

Uncomment en_US.UTF-8 UTF-8 from /etc/locale.gen

```zsh
locale-gen
```

```zsh
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

## Hostname

```zsh
echo "aurbis" > /etc/hostname
```

## USERS

1. Change the root users password
2. Install desired shell (ZSH)
3. Add New User and set default shell

```zsh
useradd -m -G wheel -s /bin/zsh [USERNAME]
```
4. Change New Users Password
5. Add the wheel group to sudoers

```zsh
EDITOR=nvim visudo
```

EX:

```zsh
%wheel ALL=(ALL:ALL) ALL
```

## Network Connectivity

```zsh
pacman -S networkmanager;
systemctl enable NetworkManager
```

## Microcode

```zsh
pacman -S amd-ucode
```

**Remake Grub Configs**

```zsh
grub-mkconfig -o /boot/grub/grub.cfg
grub-mkconfig -o /boot/efi/EFI/arch/grub.cfg
```

## Reboot

```zsh
exit;
umount -R /mnt;
reboot now
```
