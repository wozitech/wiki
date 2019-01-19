<!-- TITLE: Linux PC -->
<!-- SUBTITLE: Fedora 29 Install on Intel i7 PNY GeForce 2000 -->

# PC
* Intel i7
* 32GB DDR3 (2*16GB)
* PNG GeForce Quadro P2000 5GB graphics card
* 1TB Intel M2 SSD - boot and OS disk (it's fast!)
* 2*2TB SSD/HDD RAID disks - data disk (RAID1 - mirror for data availability).

# OS
Fedora is my preferred Linux distro. Good regular updates. Good open source community.

Fedora 29 latest at time of install. Fedora default desktop is Gnome 3; I like it, many don't. But I also like the Mate desktop too. Can install multiple desktops and switch.

However, anyone who's ever tried installing a desktop distro with one of the latest graphics cards will know its a pain; especially with UHD monitors.

## Server
So I will start with Fedora Server image. Using [Fedora Media Writer](https://getfedora.org/en/workstation/download/). Install and launch, and have a USB stick to hand (at least 8GB).

With the USB stick in target PC, boot from the stick (typically `F12` or `F9` usually to enter boot menu). At the boot prompt, edit the command line and append: `nomodeset nouveau.modeset=0`, followed by <CTRL-X> to continue.

This will launch Fedora install in text mode; use the text installer to install base image.

After install and reboot, login, and ensure latest packages are installed: `dnf update`.

## Nvidia (PNY) Display Drivers
Following this excellent guide: https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-fedora-29-linux.
1. First download the nvidia driver for Linux x86/x64 for the specific graphics card: https://www.nvidia.com/Download/index.aspx.
2. As root, install the following two package sets:

```
dnf groupinstall "Development Tools"
dnf install dkms "kernel-devel-uname-r == $(uname -r)"
```

3. Edit grub `vi /etc/default/grub` and add `nouveau.modeset=0` to the end of the `GRUB_CMDLINE_LINUX` line. Save and exit vi (`:wq`).
4. Rebuild grub as `root`:

* UEFI boot: `grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg`
* Standard boot: `grub2-mkconfig -o /boot/grub2/grub.cfg`

5. Reboot.
6. Now, because we've install Fedora server we need to install a [desktop environment](https://docs.fedoraproject.org/en-US/quick-docs/switching-desktop-environments/).
7. Before leaving the PC, enable SSH `systemctl enable sshd` and start SSH `systemctl start sshd`. By having remote SSH enabled, if after installing desktop enviroment below, the displays are inactive you can always get back onto the PC.
8. Login remotely (putty/ssh) and become `root`.
9. List the available desktops environments: `dnf grouplist -v | grep -i workstation` or `dnf grouplist -v | grep -i desktop` (for runtimes like KDE, Mate and Cinnamon.
10. Install the desktop environment, e.g. (note the "@" prefix):

```
dnf install @workstation-product-environment
```
11. Now reboot. _And cross your fingers_
12. All being good, you're boot at the login prompt. It's time to install the nvidia drivers.
13. Remotely SSH back into the PC, and become root.
14. Upload the Linux nvidia driver downloaded above (`scp`/`sftp`/`winscp`) to you home directory.
15. Then run the nividia driver installer (replacing the name of the driver as downloaded). Let nvidia install update your X configuration.
```
systemctl isolate multi-user.target
bash /home/aylingw/NVIDIA-Linux-x86_64-410.93.run
```
16. After install, start X: `startx`.
17. All being well, Gnome will start and you can start having fun.

18. To be sure that X starts every time you boot:

```
 systemctl enable gdm.service
 systemctl start gdm.service
```

19. Reboot to be sure.