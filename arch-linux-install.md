# Arch Linux Install Guide
Make sure you are installing on an empty drive. Use fdisk to delete partitions on both the USB and installation drive.

## Verify Arch Linux ISO and create bootable USB
<dl><dd>
<pre>
vim sha.txt <i># Write "[SHA256]  archlinux-[version]-x86_64.iso"</i>
sha256sum -c sha.txt
sudo pacman-key -v archlinux-[version]-x86_64.iso.sig
lsblk
fdisk /dev/sd* <i># Replace * with relevant USB output
        Command (m for help):</i> n
                <i>Partition number (1-128, default 1):</i> Enter
                <i>First sector:</i> Enter
                <i>Last sector:</i> Enter
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> y
        <i>Command (m for help):</i> w
mkfs.ext4 /dev/sd*1
sudo dd if=archlinux-[version]-x86_64.iso of=/dev/sd* status=progress bs=2M
</pre>
</dd></dl>
‎

‎
## Language and font
<dl><dd>
<pre>
localectl list-keymaps
loadkeys [keymap] <i># Default is us</i>
setfont ter-132b
</pre>
</dd></dl>
	
## Connect to Wi-fi
<dl><dd>
<pre>
iwctl
station wlan0 scan
station wlan0 get-networks
station wlan0 connect [network ID]
        <i>Passphrase: [network password]</i>
exit
</pre>
</dd></dl>
	
## Partition and format disks
<dl><dd>
<pre>
lsblk
gdisk /dev/sd* <i># Type n and then w</i>
fdisk /dev/sd*
        <i>Command (m for help):</i> n
                <i>Partition number (1-128, default 1):</i> Enter
                <i>First sector:</i> Enter
                <i>Last sector:</i> +1G
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> y
        <i>Command (m for help):</i> n
                <i>Partition number (1-128, default 2):</i> Enter 
                <i>First sector:</i> Enter 
                <i>Last sector:</i> +[at least same size as RAM]G
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> y
        <i>Command (m for help):</i> n
                <i>Partition number (1-128, default 3):</i> Enter
                <i>First sector:</i> Enter
                <i>Last sector:</i> Enter
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> y
        <i>Command (m for help):</i> w
mkfs.ext4 /dev/sd*3
mkswap /dev/sd*2
mkfs.fat -F 32 /dev/sd*1
mount /dev/sd*3 /mnt
mount --mkdir /dev/sd*1 /mnt/boot/efi
swapon /dev/sd*2
</pre>
</dd></dl>

 ## Base installation
 <dl><dd>
<pre>
pacstrap -i /mnt base base-devel efibootmgr grub linux linux-firmware vim networkmanager
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
</pre>
</dd></dl>

## Basic configuration
<dl><dd>
<pre>
ln -sf /usr/share/zoneinfo/[region]/[city] /etc/localtime
hwclock --systohc
vim /etc/locale.gen <i># Uncomment your locale, e.g. en_US.UTF-8 UTF-8</i>
locale-gen
vim /etc/locale.conf <i># Write "LANG=[locale]", e.g. LANG=en_US.UTF-8</i>
vim /etc/hostname <i># Write "[hostname]"</i>
passwd
        <i>New password:
        Retype new password:</i>
useradd -m -G wheel -s /bin/bash [username]
passwd [name]
        <i>New password:
        Retype new password:</i>
EDITOR=vim visudo <i># Uncomment "%wheel ALL=(ALL:ALL) ALL" under heading ##Uncomment to allow members of group wheel to execute any command</i>
systemctl enable NetworkManager
grub-install /dev/sd*
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -a
sync
reboot
</pre>
</dd></dl>

## Connect to Wi-Fi after reboot
<dl><dd>
<pre>
nmcli r wifi on
nmcli d wifi connect [network ID] password [network password]
</pre>
</dd></dl>
