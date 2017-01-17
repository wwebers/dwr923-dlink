# About the repository

This repository contains patches and instructions on howto successfully cross-compile the orginal sources
for the D-Link LTE router DWR923.

The supplied patches do the following:

- patch_common.txt

This patch is mainly applied to several Makefile but also some source files to enable the build of the supplied 
open source tools running under GPL.

- patch_v3_sop.txt

This patch adjusts the `timeconst.pl` inside the kernel source as well as the Makefile to enable the successful build of
the supplied version of iptables.

Additionally, this patch removes the default root password.

**Attention**: You should set the root password after flashing the firmware.

# Known limitations

Several files are not included into the GPL based source files delivered by DLink. This includes several files for
initializing and using the LTE modem.

Furthermore, it seems the sources lack necessary files for the phone unit. As such, VoIP is not possible either.

# Instructions

You can build the source only on a 32bit host system. I prefer to use Ubuntu 16.04.1 32bit running as a virtual machine on VirtualBox. You can choose another solution, of course. But the instructions will refer to my preferred setup.

## Installation of the sourcecode

### Download

After setting up the host system, you will have to download the GPL source code for the router from DLinks web page:

<http://tsd.dlink.com.tw/downloads2008detailgo.asp> (You will have to choose the correct model of the router first)

The download consist of two parts:

1. [The source code] (https://dlink-gpl.s3.amazonaws.com/GPL1400146/OG3611_GPL-SRC.1.4.17.37_20140305.tar.bz)
2. [The toolchain] (https://dlink-gpl.s3.amazonaws.com/GPL1400146/cnstools.tgz)

### Prepare the build environment

1. extract both tarballs into your build directory, for example into 'dlink' under your homedirectory:

```
mkdir dlink
tar xf cnstools.tgz -C dlink
tar xf OG3611_GPL-SRC.1.4.17.37_20140305.tar.bz -C dlink
```

2. add the cross-compiler to your PATH variable. When using `bash`, you could add the following at
the end of your `.bashrc` in your home directory.

```
export PATH=$HOME/dlink/cnstools/arm-linux-le-uclibc-gcc-4.3.3-2010.05/usr/bin/:$PATH
```

3. logout and login again to make the change effective.

## Apply the patches

1. copy the two patch files into your build directory

```
cp patch_common.txt ~/dlink
cp patch_v3_sop.txt ~/dlink
```

2. apply the patch to the source files
```
cd ~/dlink/OG3611_GPL-SRC.v1.4.17.37_20140305
patch -p 1 < ../patch_common.txt
patch -p 1 < ../patch_v3_sop.txt
```

## Build the firmware image

1. move into the source directory `v3_sop`
```
cd ~/dlink/OG3611_GPL-SRC.v1.4.17.37_20140305/v3_sop
make GPL
make release-firmware
```

2. After a successful build the flashable firmware image can be found inside the `images` directory

The resulting firmware image can be used to flash over the existing one from inside the web interface. After 
successfully flashing the firmware, you should be able to login with any password.
