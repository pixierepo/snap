# snap
Hardened kernel and modified snap profile to enable execution of snap containerized packages on Pixieboard's Arch Linux.

# Installation:

From the linux-hardened directory, use pacman to install the hardened kernel and headers:
```
sudo pacman -U linux-LTS+imx-PixiePro-4.9.135-1-armv7h.pkg.tar.xz
sudo pamcan -U linux-LTS+imx-headers-PixiePro-4.9.135-1-armv7h.pkg.tar.xz
```

Modify your /boot/boot.txt to add these boot parameters and make a new bootscript:
```
#in boot.txt, change the line below:
...
setenv display $display consoleblank=0 apparmor=1 security=apparmor
...

#From /boot directory, generate the new bootscript (make sure you have uboot-tools installed):
sudo mkimage -A arm -T script -O linux -C none -d boot.txt boot.scr
```

Install missing dependencies:
```
sudo pacman -S apparmor squashfs-tools go go-tools python-docutils
```

Install snapd from the AUR. Edit the PKGBUILD to add 'armv7h' as supported architecture:
```
yaourt snapd

#Select your desired snapd version and edit PKGBUILD.
#In PKGBUILD, make sure the arch line looks like this:
...
arch=('x86_64' 'armv7h')
...
```

From the profiles directory, copy the profile to the correct directory:
```
sudo cp usr.lib.snapd.snap-confine /etc/apparmor.d/
```

Create a symlink to enable classic mode support:
```
sudo ln -s /var/lib/snapd/snap /snap
```

Enable snapd and apparmor services:
```
sudo systemctl enable snapd.socket
sudo systemctl enable apparmor.service
```

Reboot your system.

# Usage

To test your system install the hello-world package using snap:
```
#If you get a "Too early for operation" error just wait a few seconds and try again.
sudo snap install hello-world
```

Run the package:
```
#The line below should return an error:
hello-world.evil 

#The line below should work fine:
hello-world
