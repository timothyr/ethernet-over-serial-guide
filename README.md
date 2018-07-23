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
$ sudo apt-get install -y binfmt-support qemu qemu-user-static debootstrap kpartx
$ sudo apt-get install -y lvm2 dosfstools gpart binutils git lib32ncurses5-dev python-m2crypto
$ sudo apt-get install -y gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev
$ sudo apt-get install -y autoconf libtool libglib2.0-dev libarchive-dev
$ sudo apt-get install -y python-git xterm sed cvs subversion coreutils texi2html bc
$ sudo apt-get install -y docbook-utils python-pysqlite2 help2man make gcc g++ desktop-file-utils libgl1-mesa-dev
$ sudo apt-get install -y libglu1-mesa-dev mercurial automake groff curl lzop asciidoc u-boot-tools mtd-utils
```

### Deploy source
```
$ cd ~
$ git clone https://github.com/varigit/debian-var.git -b debian_jessie_varsommx6_var02 var_som_mx6_debian
```

### Modify kernel configuration (part 1)
```
1. cd ~/var_som_mx6_debian/src/kernel
2. Run "sudo make ARCH=arm mrproper"
3. Run "sudo make ARCH=arm imx_v7_var_defconfig"
4. Run "sudo make ARCH=arm menuconfig"
```

### Enable PPP (Point-to-Point Protocol)

