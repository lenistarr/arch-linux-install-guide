# Arch Linux Install Guide
Make sure you are installing on an empty drive. Use fdisk to delete partitions on both the USB and installation drive.

## Verify Arch Linux ISO and create bootable USB
<dl><dd>
<pre>
<b>vim sha.txt</b> # Write "[SHA256]  archlinux-[version]-x86_64.iso"
<b>sha256sum -c sha.txt
sudo pacman-key -v archlinux-[version]-x86_64.iso.sig
lsblk
fdisk /dev/sd*</b> # Replace * with relevant USB output
        Command (m for help): <b>n</b>
                Partition number (1-128, default 1): <b>Enter &crarr;</b>
                First sector: <b>Enter &crarr;</b>
                Last sector: <b>Enter &crarr;</b>
                        Do you want to remove the signature? [Y]es/[N]o: <b>y</b>
        Command (m for help): <b>w</b>
<b>mkfs.ext4 /dev/sd*1
sudo dd if=archlinux-[version]-x86_64.iso of=/dev/sd* status=progress bs=2M</b>
</pre>
</dd></dl>
‎

‎
## Language and font
<dl><dd>
<pre>
<b>localectl list-keymaps
loadkeys [keymap]</b> # Default is us
<b>setfont ter-132b</b>
</pre>
</dd></dl>
	
## Connect to Wi-fi
<dl><dd>
<pre>
<b>iwctl
station wlan0 scan
station wlan0 get-networks
station wlan0 connect [network ID]</b>
        Passphrase: [network password]
<b>exit</b>
</pre>
</dd></dl>
	
## Installation
<dl><dd>
<pre>
<b>lsblk
gdisk /dev/sd*</b> # Type n and then w
<b>fdisk /dev/sd*</b>
        Command (m for help): <b>n</b>
                Partition number (1-128, default 1): <b>Enter &crarr;</b> 
                First sector: <b>Enter &crarr;</b>
                Last sector: <b>+1G</b>
                        Do you want to remove the signature? [Y]es/[N]o: <b>y</b>
        Command (m for help): <b>n</b>
                Partition number (1-128, default 2): <b>Enter &crarr;</b>
                First sector: <b>Enter &crarr;</b>
                Last sector: <b>+[at least same size as RAM]G</b>
                        Do you want to remove the signature? [Y]es/[N]o: <b>y</b>
        Command (m for help): <b>n</b>
                Partition number (1-128, default 3): <b>Enter &crarr;</b>
                First sector: <b>Enter &crarr;</b>
                Last sector: <b>Enter &crarr;</b>
                        Do you want to remove the signature? [Y]es/[N]o: <b>y</b>
        Command (m for help): <b>w</b>
<b>mkfs.ext4 /dev/sd*3
mkswap /dev/sd*2
mkfs.fat -F 32 /dev/sd*1
mount /dev/sd*3 /mnt
mount --mkdir /dev/sd*1 /mnt/boot/efi
swapon /dev/sd*2
pacstrap -i /mnt base base-devel efibootmgr grub linux linux-firmware vim networkmanager
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/[region]/[city] /etc/localtime
hwclock --systohc
vim /etc/locale.gen</b> # Uncomment your locale, e.g. en_US.UTF-8 UTF-8
<b>locale-gen
vim /etc/locale.conf</b> # Write "LANG=[locale]", e.g. LANG=en_US.UTF-8
<b>vim /etc/hostname</b> # Write "[hostname]"
<b>passwd</b>
        New password:
        Retype new password:
<b>useradd -m -G wheel -s /bin/bash [username]
passwd [name]</b>
        New password:
        Retype new password:
<b>EDITOR=vim visudo</b> # Uncomment "%wheel ALL=(ALL:ALL) ALL" under heading ##Uncomment to allow members of group wheel to execute any command
<b>systemctl enable NetworkManager
grub-install /dev/sd*
grub-mkconfig -o /boot/grub/grub.cfg
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
nmcli d wifi connect [network ID] password [network password]</b>
</pre>
</dd></dl>
