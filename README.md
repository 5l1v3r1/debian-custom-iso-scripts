# Debian Custom ISO Scripts and Tutorial
These are scripts that I made to help with the design and customization of a Debian ISO (Primarily WeakerThan Linux). This is a work in progress and will be updated with video tutorials, scripts, and lots of documentation on the process in which I created WT7 Elite.

<img src="https://weaknetlabs.com/images/dev-img.png"/>
<i>Screenshot: Weakerthan LINUX 7 "Elite" Alpha, tested in Oracle's VirtualBox.</i>

## Custom Scripts
This repository should help anyone who is unfamiliar with the process of creating a customized ISO but this is NO MEANS a full tutorial on the subject. This is just how I do it and I have thoroughly tested every step, but in the world of open-source, small changes can destroy a house of cards, so to speak. Please make sure you report errors with logs or terminal output so I can better help troubleshoot any issues that arise during your ISO building. I am making this because I honestly could not find solid, working, up-to-date tutorials anywhere I looked. I could not get Debian's "live-build" working preoperly for the life of me and it just seemed very convoluted. This tutorial and these scripts were designed to work with Debian Stretch. We will be designing our own custom 32bit Debian ISO with the SYSLINUX boot loader.

## Tutorial
The first part, is simply customizing the Debian ISO, in our case the Weakerthan LINUX flavor. The process is that same for starting from scratch, but you need to run the "initialize-build-process.sh" script which will install all of the necessary tools to build the ISO and download the Debian LINUX system including packages and system configurations using the "lb" live-build Debian tool. I actually don't recommend doing this, but if you really want to start from scratch, do it this way instead of updating an ISO.

### Updating an ISO
This process begins with a pre-existing ISO that you would like to make changes to. To start, we need an installation of Linux with lots of space and some tools installed. I figured out how to do this because, halfway through the development process of WT 7 Alpha, I lost my work. There are a few steps involved with getting a chroot envionment from an ISO and they are:
* Get the ISO
* Extract the filesystem - the root directory with /mnt, /dev, /home, /proc, /sys, /var, /etc, etc in it. This is compressed into a SquashFS in Weakerthan LINUX, but not all LINUXs. Mount the ISO as a loop device and extract the squashfs.filesystem file.
  * <code>mkdir ./mnt</code> 
  * <code>mount -o loop linux.iso ./mnt</code>
  * <code>apt-get install squashfs-tools</code>
  * <code>unsquashfs ./mnt/live/filesystem.squashfs</code>
* This will create a directory, "squashfs-root". Let's copy all of the contents in ./squashfs-root/squashfs-root into ./squashfs-root and rename it "chroot" This just cleans up our development environment and is not required.
  * <code>mv squashfs-root chroot</code>
  * <code>mv chroot/* chroot</code>
  * <code>rm -rf chroot/squashfs-root</code>
Now we have a clone of the live filesystem and we can chroot to it and customize it and make our changes. To "chroot" means to change the root directory. We will call our host OS, the "host" and the chroot envionment the "chroot" from here on. When we are done making changes in the chroot, we can simply type "exit" or CTRL+D to exit the chroot. Let's continue on and install the tools necessary to build the ISO.

<code>apt-get update</code><br />
<code>apt-get install deboostrap isolinux live-build</code>

Next, we need to download the ISO that we want to make changes to and extract the squash filesystem from it.

<code>mkdir ./mnt</code><br />
<code>mount -o loop file.iso ./mnt</code>

Next step - un-squash the squashfs and get the directory contents of "/" (root)

<code>unsquashfs ./mnt/live/squashfs.filesystem </code>

This will create a new directory called "squashfs-root" which contains all of the normal contents that you would find in a LINUX filesystem tree starting at "/" (root). We simply rename the "squashfs-root" directory to "chroot" and we have a working space to <code>chroot</code> to and make our changes in - but not before we mount some things. 

Copy the "in-chroot-scripts" directory in the "./chroot" directory so that we can access them once we have chrooted. Before we "chroot" though, we need to mount /dev/, /dev/pts, /proc, and /sys - bound to the actual mountded devices. I do this in the "chroot-script.sh" script. So, after runing the script, you will be chrooted in the new environment.

<code>./chroot-script/sh</code>

In the new chrooted envionment, we need to run the wt7-mounts.sh script. This will double check the mounted fileystems we just did and set up the enviroment, including generating a new uuid for the system. 

<code>./wt7-mounts.sh</code>

Once done with our customizations, we simply run the "wt7-umount.sh" script. This will clean up the ISO and unmount the filesystems and exit us back to the host environment.

### Creating a New Image From Scratch
This process is exactly the same as the process above, but we need to get the Debian FS, packages, and LINUX kernel before hand. We do so by running the "initialize-build-process.sh" script. DO NOT RUN THIS IF YOU ALREADY HAVE A "chroot" environment with customized changes in it. It WILL BE DESTROYED. Once done, you can go back uup to the "Updating an ISO" section and begin updating LINUX.

## References
SquashFS-Tools (Debian Package): https://packages.debian.org/search?keywords=squashfs-tools
