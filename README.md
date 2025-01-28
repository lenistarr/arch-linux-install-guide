# Arch Linux Install Guide
Make sure you are installing Arch on an empty drive. Use fdisk to delete partitions on both the USB and installation drive.

## Verify Arch Linux ISO and create bootable USB
<dl><dd>
<pre>
<b>vim sha.txt</b> <i># Write</i> "[SHA256]  archlinux-[version]-x86_64.iso"
<b>sha256sum -c sha.txt
sudo pacman-key -v archlinux-</b>[version]<b>-x86_64.iso.sig
lsblk
fdisk /dev/sd</b>* <i># Replace * with relevant USB input when running</i> <b>lsblk</b>
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 1):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>Enter</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>w</b>
<b>mkfs.ext4 /dev/sd</b>x<b>1</b>
<b>sudo dd if=archlinux-</b>[version]<b>-x86_64.iso of=/dev/sd</b>* <b>status=progress bs=2M</b>
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
gdisk /dev/sd</b>* <i># Type</i> <b>n</b> <i>and then</i> <b>w</b>
<b>fdisk /dev/sd</b>*
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 1):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>+1G</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 2):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>+</b>[enter value]<b>G</b> <i># Same as RAM, preferably double if using hibernation, e.g. if RAM is 8 GB then write</i> <b>+16G</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>n</b>
                <i>Partition number (1-128, default 3):</i> <b>Enter</b>
                <i>First sector:</i> <b>Enter</b>
                <i>Last sector:</i> <b>Enter</b>
                        <i>Do you want to remove the signature? [Y]es/[N]o:</i> <b>y</b>
        <i>Command (m for help):</i> <b>w
mkfs.ext4 /dev/sd</b>*<b>3
mkswap /dev/sd</b>*<b>2
mkfs.fat -F 32 /dev/sd</b>*<b>1
mount /dev/sd</b>*<b>3 /mnt
mount --mkdir /dev/sd</b>*<b>1 /mnt/boot/efi
swapon /dev/sd</b>*<b>2</b>
</pre>
</dd></dl>

 ## Basic installation
 <dl><dd>
<pre>
<b>pacstrap -i /mnt base base-devel efibootmgr grub linux linux-firmware vim networkmanager
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt</b>
</pre>
</dd></dl>

## Basic configuration
<dl><dd>
<pre>
<b>ln -sf /usr/share/zoneinfo/</b>[region]<b>/</b>[city]<b> /etc/localtime
hwclock --systohc
vim /etc/locale.gen</b> <i># Uncomment your locale, e.g.</i> en_US.UTF-8 UTF-8
<b>locale-gen
vim /etc/locale.conf</b> <i># Write your</i> "LANG=[locale]"<i>, e.g.</i> LANG=en_US.UTF-8
<b>vim /etc/hostname</b> <i># Write your</i> "[hostname]"<i>, e.g.</i> thinkpad
<b>passwd</b>
        <i>New password:</i> [enter your root password]
        <i>Retype new password:</i> [re-enter your root password]
<b>useradd -m -G wheel -s /bin/bash</b> [username] <i># Write your username, e.g.</i> john
<b>passwd</b> [name]
        <i>New password:</i> [enter your user password] <i># Just make it the same as your root password</i>
        <i>Retype new password:</i> [enter your user password]
<b>EDITOR=vim visudo</b> <i># Uncomment</i> "%wheel ALL=(ALL:ALL) ALL" <i>under heading</i> ##Uncomment to allow members of group wheel to execute any command</i>
<b>systemctl enable NetworkManager
grub-install /dev/sd</b>* <i># Install on whole disk, not on the individual partitions</i>
<b>grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -a
sync
reboot</b>
</pre>
</dd></dl>

## Connect to Wi-Fi after reboot
<dl><dd>
<pre>
<b>nmcli r wifi on
nmcli d wifi connect</b> [network ID] <b>password</b> [network password]
</pre>
</dd></dl>
