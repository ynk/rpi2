# Prepare SDCard with Archlinux for Raspberry PI 2 (arm7)

### Setup the environnment 

As it's not really possible to do it with OSX, let's first install VirtualBox, Vagrant and a Debian Wheezy, and connect your SDcard to the VM so we can work with it.

- Run: 

```
brew cask install virtualbox
brew install vagrant
vagrant init puppetlabs/debian-7.8-64-nocm
vagrant up && vagrant halt
```

- Plug in your SD, and launch the Disk Utility. Unmount it, but do not eject. Keep the Disk Utility running, we're gonna need it.

- Find the name of the resource by running `df -h` and look for a match with your SDcard name. It should be `/dev/disk2` or something like that.

- In order to be able to read/write, with must alter permissions on it by running `sudo chmod 777 /dev/disk2`.

- We're now gonna create a fake hard drive for VirtualBox as a reference to the sdcard. Simply run `VBoxManage internalcommands createrawvmdk -filename /tmp/sdcard.vmdk -rawdisk /dev/disk2` and voila.

**At any time from now, OSX seems to like to remount automatically the sdcard, so re-unmount it with the Disk Utility tool.**

- Now, launch VirtualBox. You may recognize a virtual machine on the list, which is the one we launched with `vagrant up && vagrant halt`. Click on configure, and go to Storage. Click on the SATA controller, and add a existing HDD, which is obviously our `/tmp/sdcard.vmdk`.

- Once done, run the vm with `vagrant up`, and log in with `vagrant ssh`.

- Install the tools we need by running `sudo apt-get update` and `sudo apt-get install -y dosfstools bsdtar`

### Archlinux

- In our debian, the sdcard should be referenced as `/dev/sdb`.

- First we need to setup the partition tables. Run `fdisk /dev/sdb`. At the prompt:

  1. Type **o**. This will clear out any partitions on the drive.
  2. Type **p** to list partitions. There should be no partitions left.
  3. Type **n**, then **p** for primary, **1** for the first partition on the drive, press **ENTER** to accept the default first sector, then type **+100M** for the last sector.
  4. Type **t**, then **c** to set the first partition to type W95 FAT32 (LBA).
  5. Type **n**, then **p** for primary, **2** for the second partition on the drive, and then press **ENTER twice** to accept the default first and last sector.
  6. Write the partition table and exit by typing **w**.  

- Move to `/tmp` by `cd /tmp`.

- Create the filesystem for the 2 partitions:

```
mkfs.vfat /dev/sdb1
mkdir foo
mount /dev/sdb1 foo
mkfs.ext4 /dev/sdb2
mkdir bar
mount /dev/sdb2 bar
```

- Download the latest archlinux and unpack it.

```
wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz
bsdtar -xpfv ArchLinuxARM-rpi-2-latest.tar.gz -C bar
sync
```

- Move the boot files to the boot partition and unmount everything.

```
mv bar/boot/* foo
umount foo bar
```

### It's run time!

- Insert the SD card into the Raspberry Pi, connect ethernet, and apply 5V power.

- Use the serial console or SSH to the IP address given to the board by your router. The default root password is 'root'.

- Update to the latest archlinux by running `pacman -Syu`
