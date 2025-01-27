# Arch Linux Install Guide
Make sure you are installing on an empty drive. Use fdisk to delete partitions on both the USB and installation drive.

## Verify Arch Linux ISO and create bootable USB
<dl><dd>
<pre>
<b>vim sha.txt</b> <i># Write</i> "[SHA256]  archlinux-[version]-x86_64.iso"
<b>sha256sum -c sha.txt
sudo pacman-key -v archlinux-</b>[version]<b>-x86_64.iso.sig
lsblk
fdisk /dev/sd</b>* <i># Replace * with relevant USB output
        Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 1):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>Enter</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>w</b>
<b>mkfs.ext4 /dev/sd*1
sudo dd if=archlinux-</b>[version]<b>-x86_64.iso of=/dev/sd* status=progress bs=2M</b>
</pre>
</dd></dl>
‎

‎
## Language and font
<dl><dd>
<pre>
<b>localectl list-keymaps
loadkeys</b> [keymap] <i># Default is</i> <b>us</b>
<b>setfont ter-132b</b>
</pre>
</dd></dl>
	
## Connect to Wi-fi
<dl><dd>
<pre>
<b>iwctl
station wlan0 scan
station wlan0 get-networks
station wlan0 connect </b>[network ID]
        <i>Passphrase:</i> [network password]
<b>exit</b>
</pre>
</dd></dl>
	
## Partition and format disks
<dl><dd>
<pre>
<b>lsblk
gdisk /dev/sd*</b> <i># Type</i> <b>n</b> <i>and then</i> <b>w</b>
<b>fdisk /dev/sd</b>*
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 1):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>+1G</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 2):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>+</b>[enter value]<b>G</b> <i># At least same size as RAM, preferably double if using hibernation</i>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 3):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>Enter</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>w
mkfs.ext4 /dev/sd*3
mkswap /dev/sd*2
mkfs.fat -F 32 /dev/sd*1
mount /dev/sd*3 /mnt
mount --mkdir /dev/sd*1 /mnt/boot/efi
swapon /dev/sd*2</b>
</pre>
</dd></dl>

 ## Basic installation
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
