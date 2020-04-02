![alt tag](https://avatars0.githubusercontent.com/u/13870012?v=3&s=200)

Intel and AMD Ryzen: This SEDutil fork includes support for intel and AMD Ryzen systems with SHA-512 password authentication

Note: This version of SEDutil is not compatible with SHA-1 versions of SEDutil

This software is Copyright 2014-2017 Bright Plaza Inc. <drivetrust@drivetrust.com>

This file is part of sedutil.

sedutil is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

sedutil is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with sedutil.  If not, see <http://www.gnu.org/licenses/>.


sedutil - The Drive Trust Alliance Self Encrypting Drive Utility

This program and it's accompanying Pre-Boot Authorization image allow
you to enable the locking in SED's that comply with the TCG OPAL 2.00
standard on bios machines.   

You must be administrator/root to run the host managment program

In Linux libata.allow_tpm must be set to 1. Either via adding libata.allow_tpm=1 to the kernel flags at boot time 
or changing the contents of /sys/module/libata/parameters/allow_tpm from a "0" to a "1" on a running system.

***** sleep (S3) is not supported.

Orginal source code is available on GitHub at https://github.com/Drive-Trust-Alliance/sedutil 

Linux and Windows executables and Linux PBA bootloader images for this version of SEDutil are available at https://github.com/ChubbyAnt/sedutil/releases

# About SEDutil for AMD Ryzen

DTA sedutil: For AMD Ryzen Systems

The sedutil project provides a CLI tool (`sedutil-cli`) capable of setting up and managing self encrypting drives (SEDs) that comply with the TCG OPAL 2.00 standard. This project also provides a pre-boot authentication image (`linuxpba`) which can be loaded onto an encrypted disk's shadow MBR. This pre-boot authentication image allows the user enter their password and unlock SED drives during the boot process. **Using this tool can make data on the drive inaccessible!**


## Setup

To configure a drive, load a compatible [RECOVERY](https://github.com/ChubbyAnt/sedutil/releases) image onto a USB drive and follow the instructions here:  

https://github.com/Drive-Trust-Alliance/sedutil/wiki/Encrypting-your-drive  


## Origin

This version of sedutil is based off the original [@dta](https://github.com/Drive-Trust-Alliance/sedutil/) implementation incorporating some modifications by [@ladar](https://github.com/ladar/sedutil), [@ckamm](https://github.com/ckamm/sedutil/) and [@CyrilVanErsche](https://github.com/CyrilVanErsche/sedutil/). This fork adds support for the PBA bootloader on AMD Ryzen and AMD Ryzen mobile systems.


## Notable Differences

Unique to this repo are the following modifications:

* SHA512 password hashing vs SHA1 on original SEDutil
* Compatibile with AMD Ryzen and AMD Ryzen mobile systems


## Build Process

Building is supported on Ubuntu 18.04.3 (LTS) x64. Other versions will probably not compile correctly!

To compile your own version of `sedutil` you will need the standard development tools, an internet connection, and ~10 GB of disk space. 

### Prerequisites:  

```
sudo apt-get update && sudo apt-get upgrade -y  
  
sudo apt-get install build-essential autoconf pkg-config libc6-dev make g++-multilib m4 libtool ncurses-dev unzip zip git python zlib1g-dev wget bsdmainutils automake curl bc rsync cpio git nasm -y

```

### Automatically build everything:  

```
git clone https://github.com/ChubbyAnt/sedutil && cd sedutil && autoreconf --install && ./configure && make all && cd images && ./getresources && ./buildpbaroot && ./buildbios && ./buildUEFI64 && ./buildrescue Rescue32 && ./buildrescue Rescue64 && cd ..
```

### Build everything manually step by step:  

```
git clone https://github.com/ChubbyAnt/sedutil
cd sedutil
autoreconf --install
./configure
make all
cd images
./getresources
./buildpbaroot
./buildbios
./buildUEFI64
./buildrescue Rescue32
./buildrescue Rescue64
cd ..
```

The various recovery and boot images will be located in the `images` directory.


## Testing

This version has only been verified to boot on a HP x360 Envy AMD 3700u with a Samsung EVO 970 Plus 2TB NVMe drive. Testing has also focused only on the 64 bit UEFI images. While the other variants might work, you should exercise caution, and if possible, test the release on a computer with data that is expendable.

## Encrypting Your Drive

For the most comprehensive information, review this first:  

https://github.com/Drive-Trust-Alliance/sedutil/wiki/Encrypting-your-drive  

Both the PBA and rescue systems use the us_english keyboard. This can cause issues when setting the password on your normal operating system if you use another keyboard mapping. To make sure the PBA recognizes your password you are encouraged to set up you drive from the rescue system as described on this page.

### Prepare a bootable rescue system

These are the instructions for modern UEFI NVME equipped systems using SEDutil OPAL locking and unlocking utility as a windows pre-boot bootloader:

Download the rescue system for 64bit UEFI  
 
* UEFI support currently requires that Secure Boot be turned off

Transfer the Rescue image to the USB stick with a program like [Balena Etcher](https://www.balena.io/etcher/).

Restart your computer, enter the BIOS, and disable secure boot.  
Note: Earlier versions of SEDutil also required BIOS enable of "legacy boot" or "CSM" or "Compatility Mode" - this is no longer required with this version of SEDutil. 

Boot the USB thumb drive with the rescue system on it. You will see the Login prompt, enter "root" there is no password so you will get a root shell prompt.

Enter the command ```sedutil-cli --scan```  

Expected Output:

```
#sedutil-cli --scan
Scanning for Opal compliant disks
/dev/nvme0  2  Samsung SSD 960 EVO 250GB                2B7QCXE7
/dev/sda    2  Crucial_CT250MX200SSD1                   MU04    
/dev/sdb   12  Samsung SSD 850 EVO 500GB                EMT01B6Q
/dev/sdc    2  ST500LT025-1DH142                        0001SDM7
/dev/sdd   12  Samsung SSD 850 EVO 250GB                EMT01B6Q
No more disks present ending scan
```

Verify that your drive has a 2 in the second column indicating OPAL 2 support. If it doesn't do not proceed, there is something that is preventing sedutil from supporting your drive. If you continue you may erase all of your data.

### Test the PBA

Enter the command ```linuxpba``` and use a pass-phrase of ```debug```. If you don't use debug as the pass-phrase your system will reboot!
Expected Output:

```
#linuxpba 

DTA LINUX Pre Boot Authorization 


Please enter pass-phrase to unlock OPAL drives: *****
Scanning....
Drive /dev/nvme0 Samsung SSD 960 EVO 250GB                is OPAL NOT LOCKED   
Drive /dev/sda   Crucial_CT250MX200SSD1                   is OPAL NOT LOCKED   
Drive /dev/sdb   Samsung SSD 850 EVO 500GB                is OPAL NOT LOCKED   
Drive /dev/sdc   ST500LT025-1DH142                        is OPAL NOT LOCKED   
Drive /dev/sdd   Samsung SSD 850 EVO 250GB                is OPAL NOT LOCKED   
```

Verify that Your drive is listed and the that the PBA reports it as "is OPAL"

Issuing the commands in the steps that follow will enable OPAL locking. If you have a problem you will need to follow the steps at the end of this page [Recovery Information](https://github.com/Drive-Trust-Alliance/sedutil/wiki/Encrypting-your-drive#recovery-information) to either disable or remove OPAL locking.

The following steps use /dev/nvme0 as the device and UEFI64-1.15.img.gz for the PBA image, substitute the proper /dev/nvme? for your drive and the proper PBA name for your system

### Enable Locking and the PBA  

Enter the commands below: (Use the password of debug for this test, it will be changed later)  
```
gunzip /usr/sedutil/UEFI64-*img.gz 
sedutil-cli --initialsetup debug /dev/nvme0
sedutil-cli --enablelockingrange 0 debug /dev/nvme0
sedutil-cli --setlockingrange 0 lk debug /dev/nvme0
sedutil-cli --setmbrdone off debug /dev/nvme0
sedutil-cli --loadpbaimage debug /usr/sedutil/UEFI64-*.img /dev/nvme0 
```
Expected Output:  

```
#sedutil-cli --initialsetup debug /dev/nvme0
- 14:06:39.709 INFO: takeOwnership complete
- 14:06:41.703 INFO: Locking SP Activate Complete
- 14:06:42.317 INFO: LockingRange0 disabled 
- 14:06:42.694 INFO: LockingRange0 set to RW
- 14:06:43.171 INFO: MBRDone set on 
- 14:06:43.515 INFO: MBRDone set on 
- 14:06:43.904 INFO: MBREnable set on 
- 14:06:43.904 INFO: Initial setup of TPer complete on /dev/nvme0
#sedutil-cli --enablelockingrange 0 debug /dev/nvme0
- 14:07:24.914 INFO: LockingRange0 enabled ReadLocking,WriteLocking
#sedutil-cli --setlockingrange 0 lk debug /dev/nvme0
- 14:07:46.728 INFO: LockingRange0 set to LK
#sedutil-cli --setmbrdone off debug /dev/nvme0
- 14:08:21.999 INFO: MBRDone set off 
#gunzip /usr/sedutil/UEFI64-1.15.img.gz 
#sedutil-cli --loadpbaimage debug /usr/sedutil/UEFI64-1.15.img /dev/nvme0
- 14:10:55.328 INFO: Writing PBA to /dev/nvme0
33554432 of 33554432 100% blk=1500 
- 14:14:04.499 INFO: PBA image  /usr/sedutil/UEFI64.img written to /dev/nvme0
#
```

### Test the PBA (yes again)  

Enter the command ```linuxpba``` and use a pass-phrase of debug  

This second test will verify that your drive really does get unlocked.  
Expected Output:  

```
#linuxpba 

DTA LINUX Pre Boot Authorization 


Please enter pass-phrase to unlock OPAL drives: *****
Scanning....
Drive /dev/nvme0 Samsung SSD 960 EVO 250GB                is OPAL Unlocked   <---  IMPORTANT!!  
Drive /dev/sda   Crucial_CT250MX200SSD1                   is OPAL NOT LOCKED   
Drive /dev/sdb   Samsung SSD 850 EVO 500GB                is OPAL NOT LOCKED   
Drive /dev/sdc   ST500LT025-1DH142                        is OPAL NOT LOCKED   
Drive /dev/sdd   Samsung SSD 850 EVO 250GB                is OPAL NOT LOCKED   
```

Verify that the PBA unlocks your drive, it should say "is OPAL Unlocked" If it doesn't then you will need to follow the steps at the end of this page to either remove OPAL or disable locking.  

### Set a real password  

The SID and Admin1 passwords do not have to match but it makes things easier.  
```
edutil-cli --setsidpassword debug yourrealpassword /dev/nvme0
sedutil-cli --setadmin1pwd debug yourrealpassword /dev/nvme0
```

Expected Output:  

```
#sedutil-cli --setsidpassword debug yourrealpassword /dev/nvme0
#sedutil-cli --setadmin1pwd  debug yourrealpassword /dev/nvme0
- 14:20:53.352 INFO: Admin1 password changed
```

Make sure you didn't mistype your password by testing it.  

```sedutil-cli --setmbrdone on yourrealpassword /dev/nvme0```

Expected Output:  

```
#sedutil-cli --setmbrdone on yourrealpassword /dev/nvme0
- 14:22:21.590 INFO: MBRDone set on 
```

**Your drive is now using OPAL locking.**

You now need to COMPLETELY POWER DOWN YOUR SYSTEM  
This will lock the drive so that when you restart your system it will boot the PBA.

## Recovery information:  

If there is an issue after enabling locking you can either disable locking or remove OPAL to continue using your drive without locking.  

**If you want to disable Locking and the PBA:**  

```
sedutil-cli -–disableLockingRange 0 <password> <drive>  
sedutil-cli –-setMBREnable off <password> <drive>
```

Expected Output:  
```
#sedutil-cli --disablelockingrange 0 debug /dev/nvme0
- 14:07:24.914 INFO: LockingRange0 disabled 
#sedutil-cli --setmbrenable off debug /dev/nvme0
- 14:08:21.999 INFO: MBREnable set off 
```

You can re-enable locking and the PBA using this command sequence:  

```
sedutil-cli -–enableLockingRange 0 <password> <drive>      
sedutil-cli –-setMBREnable on <password> <drive>  
```

Expected Output:  

```
#sedutil-cli --enablelockingrange 0 debug /dev/nvme0
- 14:07:24.914 INFO: LockingRange0 enabled ReadLocking,WriteLocking
#sedutil-cli --setmbrenable on debug /dev/nvme0
- 14:08:21.999 INFO: MBREnable set on 
```

Some OPAL drives have a firmware bug that will erase all of your data if you issue the commands below. See [Remove OPAL](https://github.com/Drive-Trust-Alliance/sedutil/wiki/Remove-OPAL) for a list of drive/firmware pairs that is know to have been tested.  

**To remove OPAL issue these commands:**

```
sedutil-cli --revertnoerase <password> <drive>
```

Expected Output:  

```
#sedutil-cli --revertnoerase debug /dev/nvme0
- 14:22:47.060 INFO: Revert LockingSP complete
```

Verify that the locking SP has been deactivated:  
```
sedutil-cli --query {drive}
```  

Look at the query output and make certain that the Locking section shows ```lockingEnabled=N```

```  
-----
Locking function (0x0002)
Locked = N, LockingEnabled = N, LockingSupported = Y,
---------
```  

If the query does not show lockingEnabled=N DO NOT CONTINUE with the next step, if you do all your data will be erased.  

Remove OPAL:  
```
sedutil-cli --reverttper {SIDpassword} {drive}
```

Expected output:  

```
#sedutil-cli --reverttper debug /dev/nvme0
- 14:23:13.968 INFO: revertTper completed successfully
#
```

When this is finished the drive will be in a non-opal managed state. This would allow you to do anything that you could have done before starting OPAL management under OPAL. You can also reinitiate OPAL management if you wish.
  
  
  
