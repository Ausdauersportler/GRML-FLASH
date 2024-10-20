# GRML-FLASH

GRML Linux based flash tool with flashrom, amdvbflash, nvflash 

Download disk image (check releases on this page) and restore it using the [Balena Etcher tool](https://www.balena.io/etcher/) onto an USB or SD device of at least 1 GB size.

The purpose of this tool is to use `amdvbflash` or `nvflash` to read existing or write new Video BIOS versions to graphics adapters or to save and modify (Apple iMac) firmware using `flashrom` and `UEFIPatch` to enable EFI boot screen support for Apple iMac models listed below. Please note the most recent Video BIOS versions will be found on this separate [repository](https://github.com/Ausdauersportler/IMAC-EFI-BOOT-SCREEN). You need to download the file you need and add it manually to the tool provided here. Supported iMac models are:

iMac9,1 early 2009 24 inch (2,93GHz and 3,06GHz models, only)

iMac10,1 late 2009 21.5 inch

iMac10,1 late 2009 27 inch

iMac11,1 late 2009

iMac11,2 and iMac11,3 mid 2010

iMac12,1 and iMac12,2 mid 2011

Of course this tool can be used on any other Intel based system like MacPro4,1 or MacPro5,1 or other normal PCs!

## Booting from the USB 

If your Mac has not EFI capable graphics card installed you should simply disconnect all other drives from all connectors. On power on the Mac will scan the USB bus and find and boot the prepared Linux USB. 

## Remote Access via SSH

In case you have a black screen scenario with a non working graphics card detach all bootable disk devices, attach your iMac via ethernet directly to a router providing a DHCP service and force booting form the USB tool. Check your router for the IP address (e.g. 192.168.1.20) provided to your iMac and access the Linux distribution remotly from another PC, Mac, iPad - literally any device running an SSH client can be used. Default password of user `root` is `flash`. Access is possible through (replace IP with your address)

`
% ssh root@IP 
`
## Working directory with persistence

The tool supports persistence, i.e. dumps from your BIOS or video BIOS can be saved on the USB device in the particualar working directory as described below.

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
% cd /lib/live/mount/persistence/sda/flash/Video 
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
% cd /lib/live/mount/persistence/sda/flash/Firmware
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

The tool uses FAT32 to achieve double compability. It can be booted on any Mac without installing another boot loader and you have read and write access from the Apple Finder. GRML uses normally Linux ext2, so we have to boot strap through two stages folling this [guide](https://forums.macrumors.com/threads/imac-2011-see-more-uefi-firmware-mod.2257435/post-31093603) by using the existing GRML tool. Download a new version of GRML [here](https://grml.org). Upcoming version will be 2022.11.

## License

[GRML](https://grml.org) is a packaged Linux distribution based on [Debian](https://www.debian.org). Debian [license rules](https://www.debian.org/legal/index.en.html) can be found on the Debian project page.

Please look up the license rules of the mainly used software like [Flashrom](https://www.flashrom.org/Flashrom), [amdvbflash](https://www.techpowerup.com/download/ati-atiflash/) and [nvflash](https://www.techpowerup.com/download/nvidia-nvflash/).

## Credits

Original version has been created by [xanderon](https://forums.macrumors.com/members/1201679/) on MacRumors.

## Support

If you want to support the AMD (i)Mac vBIOS development [donations](https://www.paypal.com/paypalme/Ausdauersportler) are greatly appreciated.
