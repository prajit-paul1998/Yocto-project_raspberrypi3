# Yocto Project

Build an image for Raspberrypi3 using Yocto project

## Steps 
1. Install needed tools
2. Download dependencies 
3. Clone Poky
4. Add meta layers for raspberry pi 
5. Setup build config
6. Extra raspberry hardware configration
7. Extra raspberry software configration
8. Bitbake the image
9. Extract the image to the SD-card

 > ## Install Needed Tools
## First you need to install the following tools
 ```
sudo apt-get install wegt git-core tar unzip build-essential gcc-multilib chrpath gawk patch diffstat textinfo sed lz4 python3-distutils zstd libncurses5-dev bison flex make
 ```
Make directory for all the files (for example)"sources"
```
mkdir sources
cd sources
```

 > ## CLONE POKY VERSION
you need to clone poky
```
git clone https://git.yoctoproject.org/poky.git -b dunfell
```

 > ## ADDITIONAL META LAYERS FOR RASPBERRY PI
 clone the following repositories: meta-raspberry, meta-openembedded
```
git clone https://git.yoctoproject.org/meta-raspberrypi.git -b dunfell
```
```
git clone https://git.openembedded.org/meta-openembedded -b dunfell
```

GET INSIDE "poky" DIRECTORY
```
cd poky
```

 > ## SETUP BUILD CONFIG
 You need to initialize the enviroment of the project to generate "build" directory
 ```
 source oe-init-build-env build
 ```
GET INSIDE "build" then "conf" DIRECTORY
```
cd build
```
```
cd conf
```
> ## FIRST ADD THE META-RASPBERRY IN THE BBLAYERS.CONF FILE
 then you must add the other layers to your project by adding their apsolute path to "bblayers.conf" file
 
 ```
 gedit bblayers.conf
 ```
 
 ```
  BBLAYERS ?= " \
  /home/saher/Saher-RPI3/RPI3-IMAGE/poky/meta \
  /home/saher/Saher-RPI3/RPI3-IMAGE/poky/meta-poky \
  /home/saher/Saher-RPI3/RPI3-IMAGE/poky/meta-yocto-bsp \
  /home/saher/Saher-RPI3/RPI3-IMAGE/meta-raspberrypi \
  /home/saher/Saher-RPI3/RPI3-IMAGE/meta-openembedded/meta-oe \
  /home/saher/Saher-RPI3/RPI3-IMAGE/meta-openembedded/meta-python \
  "
 ```
> ## SECOND EDIT THE LOCAL.CONF
```
gedit local.conf
```
Configure the machine to "raspberrypi3" in "local.conf" file
Depending on which Raspberry you want to use (raspberrypi0, raspberrypi0w, raspberrypi3, raspberrypi3-64, raspberrypi4, raspberrypi4-64, etc)

```
MACHINE ??= "raspberrypi3"
```
> ## UNCOMMENT THE FOLLOWING LINES 
 ```
DL_DIR ?= "${TOPDIR}/downloads"
SSTATE_DIR ?= "${TOPDIR}/sstate-cache"
TMPDIR = "${TOPDIR}/tmp"
 ```

> ## EXTRA RASPBERRY HARDWARE CONFIGURATIONS
To set specific hardware settings, you can have a look to extra-apps.md and extra-build-config.md. You find this files also in the meta-raspberrypi/docs directory.

The meta layer also provides a image configuration "rpi-test-image" to use with bitbake. The image is based on "core-image-base" which includes most of the packages in meta-raspberrypi and some media samples.
 ```
bitbake -k rpi-test-image
 ```
 
> ## EXTRA SOFTWARE CONFIGURATIONS
Depending on what image build configuration you are using, you might need to install additional software packages.
You can do this, by adding some settings to the local.conf file.
For example add the following lines, to set ssh-server, pi-user and systemd:
 ```
## packages
IMAGE_INSTALL:append = " openssh-sftp-server sudo python3 python3-pip rpi-gpio raspi-gpio"
IMAGE_FEATURES:append = " ssh-server-openssh"

## systemd settings
DISTRO_FEATURES:append = " systemd"
VIRTUAL-RUNTIME:init_manager = "systemd"
VIRTUAL-RUNTIME:initscripts = ""
IMX_DEFAULT_DISTRO_FEATURES:append = " systemd"
 ```
> ## Or add python only:
  ```
 IMAGE_INSTALL:append = " python3 python3-pip rpi-gpio raspi-gpio"
  ```


> ## FLASH CONFIGRATION TO SD-CARD
Add the following lines at the end, to get a sdimg to flash to a SD-card:
```
IMAGE_FSTYPES = "ext4.xz rpi-sdimg"
```
```
SDIMG_ROOTFS_TYPE = "ext4.xz"
```


 > ## BITBAKE THE IMAGE 
 Now you have settings to build the image:-
1. core-image-minimal: A small image just capable of allowing a device to boot.
2. core-image-base: A console-only image that fully supports the target device hardware.
3. core-image-full-cmdline: A console-only image with more full-featured Linux system functionality installed.
 ```
 bitbake core-image-base
 ```
 
 > ## EXTRACT THE IMAGE TO THE SD-CARD
 After the build of the project finish successfully you will find your image in path "/home/saher/Saher-RPI3/RPI3-IMAGE/poky/build/tmp/deploy/images/raspberrypi3/"  
 to extract it to your SD card you simply can use "dd" comand but firstly you must get your sd-card id carfully and this is important to not remove another partion or disk
 
Format your sd-card
To show all disks use this command
```
sudo fdisk -l
```
To know your sd-card id you can use command
```
lsblk
```
For format the sd-card you need to unmount it
```
unmount /dev/sdb1
```
```
unmount /dev/sdb2
```
Now we need tool to burn the image 
```
sudo apt-get install bmap-tools
```
Get inside in this directory
```
cd /home/saher/Saher-RPI3/RPI3-IMAGE/poky/build/tmp/deploy/images/raspberrypi3/
```
Then use this command to burn on sd-card
```
sudo bmaptool copy core-image............tar.baz2 --bmap  core-image.........rootfs.wic.bmap /dev/sdb
```
 Or use this command
```
 sudo dd if=core-image-minimal-raspberrypi3.rpi-sdimg of=/dev/sdb
```
