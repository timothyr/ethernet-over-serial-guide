# Ethernet Over Serial Guide

## Getting Started

We will use PPP (Point-to-Point-Protocol) to give a Variscite access to ethernet with only a serial connection.

### Aliases

* ‘host’: The Variscite that has access to ethernet via an Ethernet cable.
* ‘device’: The Variscite that does not have access to ethernet.

### Materials Needed

* 2x Variscite SOM
* 3x Jumper cable
* 1x PC with Debian or Ubuntu installed - used for building Kernel
* 1x 4gb or larger SD card
* 1x SD card reader

### Kernel Modifications

At the time of writing, we are using Debian Jessie Release 2 - 4.1.15.
By default, the Kernel does not have PPP enabled.

To enable PPP, we must rebuild the Kernel.

## Building the New Kernel

Follow the guide here: http://variwiki.com/index.php?title=VAR-SOM-MX6_Debian_R2
For convenience, here is a copy of the commands to run:

(Assuming your machine is running Debian jessie, see guide for Ubuntu packages if you're using Ubuntu)

### Install required packages
```
sudo apt-get install -y binfmt-support qemu qemu-user-static debootstrap kpartx
sudo apt-get install -y lvm2 dosfstools gpart binutils git lib32ncurses5-dev python-m2crypto
sudo apt-get install -y gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev
sudo apt-get install -y autoconf libtool libglib2.0-dev libarchive-dev
sudo apt-get install -y python-git xterm sed cvs subversion coreutils texi2html bc
sudo apt-get install -y docbook-utils python-pysqlite2 help2man make gcc g++ desktop-file-utils libgl1-mesa-dev
sudo apt-get install -y libglu1-mesa-dev mercurial automake groff curl lzop asciidoc u-boot-tools mtd-utils
```

### Deploy source
```
$ cd ~
$ git clone https://github.com/varigit/debian-var.git -b debian_jessie_varsommx6_var02 var_som_mx6_debian
```

### Modify kernel configuration (part 1)
From "12 How-to: Modify kernel configuration"
```
cd ~/var_som_mx6_debian/src/kernel
sudo make ARCH=arm mrproper
sudo make ARCH=arm imx_v7_var_defconfig
sudo make ARCH=arm menuconfig
```

### Enable PPP (Point-to-Point Protocol)
* Navigate to Device Drivers
* Navigate to Network Device Support
* Go to everything PPP related and Press 'Y' so it has a (*) (should look similar to image below)
* Save Kernel by using Arrow key -> Right until the bottom text hovers over (S)ave
* Press Enter to Save. Do not fill out a name for the config file. Press enter again.
* Exit the menu config

![PPP in Kernel](ppp-debian-stretch.png?raw=true "PPP in Kernel")

### Modify kernel configuration (part 2)
From "12 How-to: Modify kernel configuration"
```
sudo make ARCH=arm savedefconfig
sudo cp arch/arm/configs/imx_v7_var_defconfig arch/arm/configs/imx_v7_var_defconfig.orig
sudo cp defconfig arch/arm/configs/imx_v7_var_defconfig
```

### Build the kernel
Do NOT run the commands in "4.1 Build all". It will overwrite the config you just modified.

Run all commands in "4.2 Build by parts"
```
cd ~/var_som_mx6_debian
./make_var_som_mx6_debian.sh -c bootloader
./make_var_som_mx6_debian.sh -c kernel
./make_var_som_mx6_debian.sh -c modules
./make_var_som_mx6_debian.sh -c rootfs
./make_var_som_mx6_debian.sh -c rtar
```

### Create boot SD card
1) Plug SD card into machine
2) Run fdisk -l to find where the SD card is
![fdisk -l](fdisk.png?raw=true "fdisk -l")
3) Outlined in red is my SD card. It is at /dev/sdd
4) Run command below but replace /dev/sdX with your SD card location (e.g. /dev/sdd)

```
./make_var_som_mx6_debian.sh -c sdcard -d /dev/sdX
```

### Done
You have an SD card with the Point-to-Point Protocol enabled.

* Plug the SD card into the Variscite MX6 Custom board. 
* Hold the middle button and switch the power on
* The board will boot from the SD card

### Optional - Flash SD card to internal memory (eMMC)
While logged into the Variscite (user: linaro pass: linaro) run the following commands:
```
sudo su
debian-install.sh -b mx6cb -t res
```

