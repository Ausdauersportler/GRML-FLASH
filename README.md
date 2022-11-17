# GRML-FLASH

GRML Linux based flash tool with flashrom, amdvbflash, nvflash 

Download disk image and restore it using the [Balena Etcher tool](https://www.balena.io/etcher/) onto an USB or SD device of at least 2GB size.

The purpose of this tool is to use `amdvbflash` or `nvflash` to write new Video BIOS versions to graphics adapters or to save and modify (Apple iMac) firmware using `flashrom` and `UEFIPatch` to enable EFI boot screen support for Apple iMac models

iMac11,1 late 2009

iMac11,2 and iMac11,3 mid 2010

iMac12,1 and iMac12,2 mid 2011

Of course this tool can be used on any other Intel based system.

The tool supports persistence, i.e. dumps from your BIOS or video BIOS can be saved on the USB device in the particualar workind directorey as described below.

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

To use the BIOS flashing tools change working directory to:

`
% cd /lib/live/mount/persistence/sda/flash/Firmware
`
