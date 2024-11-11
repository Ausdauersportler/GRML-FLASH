# GRML-FLASH

GRML Linux based flash tool with flashrom, amdvbflash, and nvflash. 

Download disk image (check releases on this page) and restore it using the [Balena Etcher tool](https://www.balena.io/etcher/) onto an USB or SD device of at least 1 GB size. Unfortunately this method creates a 1GB filesystem with limited free space, enough to store your Macs firmware and your video bios image. If you have a USB device with more capacity format it using FAT (not ExFAT!), just open the image in Finder, and copy all folders over to the new and bigger drive. 

The purpose of this tool is to use `amdvbflash` or `nvflash` to read existing or write new video BIOS versions to graphics adapters or to save and modify (Apple iMac) firmware using `flashrom` and `UEFIPatch` to enable EFI boot screen support for Apple iMac models listed below. 

### Contents

Please note the most recent graphics card BIOS versions will be found on this separate [repository](https://github.com/Ausdauersportler/IMAC-EFI-BOOT-SCREEN). You will have to download the file you need separately and add it manually to the tool provided here, the folder flash -> video is the best place to save it.

You can also update `amdvbflash` and `nvflash` manually, I will not create new releases each time a new software version hits the internet.

### Supported hardware

Supported iMac models are:

iMac9,1 early 2009 24 inch (2,93GHz and 3,06GHz models, only)

iMac10,1 late 2009 21.5 inch

iMac10,1 late 2009 27 inch

iMac11,1 late 2009

iMac11,2 and iMac11,3 mid 2010

iMac12,1 and iMac12,2 mid 2011

Of course this tool can be used on any other Intel based system like MacPro3,1, MacPro4,1 or MacPro5,1 or even on normal PCs! 

Supported graphics hardware includes all ATI/AMD and NVIDIA cards usable in Mac Intel hardware. Most recent graphics cards may need updated versions of amdvbflash or nvflash. 

## Booting from the USB 

If your Mac has no EFI capable graphics card installed you should simply disconnect all other drives from all connectors. On power on the Mac will scan the USB bus and find and boot the prepared Linux USB. 

## Remote Access via SSH

In case you have a black screen scenario with a non working graphics card detach all bootable disk devices, attach your iMac via ethernet directly to a router providing a DHCP service and force booting form the USB tool. Check your router for the IP address (e.g. 192.168.1.20) provided to your iMac and access the Linux distribution remotly from another PC, Mac, iPad - literally any device running an SSH client can be used. Default password of user `root` is `flash`. Access is possible through (replace IP with your address)

`
% ssh root@IP 
`
## Working directory with persistence

The tool supports persistence, i.e. dumps from your BIOS or video BIOS can be saved on the USB device in the particualar working directory as described below. Please note v2.0.0 has folder names changed from `Video` to `video` and `Firmware` to `firmware` (good old Unix habit).

After booting configure the local keyboard and check for the working directory using the `lsblk` command. 

`
% lsblk
`

`
/lib/live/mount/persistence/sda
`

Note this working folder may change on different systems and on the same system on reboot. Run `lsblk` each time again!

To use the video flashing tools change working directory to:

`
% cd /lib/live/mount/persistence/sda/flash/video 
`

## AMD graphcis card flashing

AMD card users can run the locally included amdvbflash software like this to save the existing vBIOS named OldBios.rom into the Backups folder

`
% ./amdvbflash -s 0 Backups/OldAMDBios.rom 
`

Flashing a new video BIOS stored in file NewAMDBIOS.rom from folder AMD

`
% ./amdvbflash -p 0 AMD/NewAMDBios.rom 
`

In case you find a more recent BIOS already on your card you can force writing an older version using

`
% ./amdvbflash -f -p 0 AMD/NewAMDBios.rom 
`

Reboot after flashing the video BIOS.

## NVIDIA graphcis card flashing

Saving the original version to the Backups folder

`
./nvflash --save Backups/OldNVBios.rom
`

and for flashing/writing a new video BIOS `NewNVBIOS.rom`from folder NVIDIA

```
% ./nvflash --protectoff

% ./nvflash -6 NVIDIA/NewNVBIOS.rom

% ./nvflash --verify NVIDIA/NewNVBIOS.rom
```
Reboot after flashing the video BIOS.

## Using flashrom to save and write the iMac firmware (BIOS)

To use the iMac/Mac BIOS flashing tools change working directory to:

`
% cd /lib/live/mount/persistence/sda/flash/firmware
`
and

`
% flashrom --programmer internal -r my_firmware.bin
`

The resulting file `my_firmware.bin` has a size of 4MB until 2010 and 8MB with 2011 iMac models. Early 2009 systems have no write protections and you can write back a modified versions of the firmware easily using:

`
% flashrom --programmer internal -w my_modified_firmware.bin
`

## Accessing saved files from macOS 

Your saved video BIOS files can be found on the USB (Windows FAT32 format) under flash -> Video -> Backups assuming your have used the linux terminal commands as described above. Firmware files can be found in flash -> Firmware.

If you plan to write a new AMD video BIOS to your card just copy the new image into the flash -> Video -> AMD in advance.

## Rebuilding a new GRML 

The tool uses FAT32 to achieve double compability. It can be booted on any Mac or PC without installing another boot loader and you have read and write access from the Apple Finder or Windows desktop. GRML uses normally Linux ext2, so we have to boot strap through two stages folling this [guide](https://forums.macrumors.com/threads/imac-2011-see-more-uefi-firmware-mod.2257435/post-31093603) or the condensed description below by using the existing GRML tool. First download a new/recent version of GRML [here](https://grml.org). Recent version is named 2024.02 and it has Navi23 drivers.

Put the ISO image either into the flash folder and navigate there as usual after booting Linux or download it using ssh into the `/root` folder. 

Now plug in another prepared USB or SD-card (FAT32/GUID) and check the `/etc/vfstab` file of your currently booted Linux, the last line will reveal the device where the newly plugged in device can be accessed. Usually I name the new device differently like `NEWFLASH`.
Finally start the process by entering the command line below (assuming `/dev/sdc2` is the device of choice):

`
% grml2usb --bootoptions="keyboard=de ssh=flash persistence" --skip-bootflag grml64-full_2021.07.iso /dev/sdc2
`

Just to confuse you all, the `keyboard=de` sets keyboard layout to German, which I usually have around here. You can either delete the keyboard setting or adapt it to your local needs. The `ssh=flash` sets the ssh password to `flash`, the `persistence` flag makes the USB writable after booted into Linux to save PC/Mac firmware or graphics card firmware aka vBIOS files.

Finally you can mount the USB on your macOS system and add copy over the flash folder from the old version or create a new one to have a fresh start. All releases here have a prepared `flash` folder with some binaries and other files.

![flash folder and contents](https://github.com/Ausdauersportler/IMAC-EFI-BOOT-SCREEN/blob/main/images/GRML-FLASH.png)

## License

[GRML](https://grml.org) is a packaged Linux distribution based on [Debian](https://www.debian.org). Debian [license rules](https://www.debian.org/legal/index.en.html) can be found on the Debian project page.

Please look up the license rules of the mainly used software like [Flashrom](https://www.flashrom.org/Flashrom), [amdvbflash](https://www.techpowerup.com/download/ati-atiflash/) and [nvflash](https://www.techpowerup.com/download/nvidia-nvflash/).

## Credits

Original version has been created by [xanderon](https://forums.macrumors.com/members/1201679/) on MacRumors.

## Support

If you want to support the AMD (i)Mac vBIOS development [donations](https://www.paypal.com/paypalme/Ausdauersportler) are greatly appreciated.
