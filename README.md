# GRML-FLASH

GRML Linux based flash tool with flashrom, amdvbflash, nvflash 

Download disk image and restore it using the [Balena Etcher tool](https://www.balena.io/etcher/) onto an USB or SD device of at least 2GB size.

The purpose of this tool is to use `amdvbflash` or `nvflash` to write new Video BIOS versions to graphics adapters or to save and modify (Apple iMac) firmware using `flashrom` and `UEFIPatch` to enable EFI boot screen support for Apple iMac models

iMac11,1 late 2009

iMac11,2 and iMac11,3 mid 2010

iMac12,1 and iMac12,2 mid 2011

Of course this tool can be used on any other Intel based system.

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
## NVIDIA graphcis card flashing

Saving the original version to the Backups folder

`
./nvflash --save Backups/OldNVBios.rom
`

and

```
% ./nvflash --protectoff

% ./nvflash -6 NewNVBIOS.rom

% ./nvflash --verify
```
Reboot after flashing the video BIOS.

## Using flashrom to save and write the iMac firmware (BIOS)

To use the iMac/Mac BIOS flashing tools change working directory to:

`
% cd /lib/live/mount/persistence/sda/flash/Firmware
`

## ssh remote access

In case you have a black screen scenario with a non working graphics card detach all bootable disk devices, attach your iMac via ethernet directly to a router providing a DHCP service and force booting form the USB tool. Check your router for the IP address (e.g. 192.168.1.20) provided to your iMac and access the Linux distribution remotly from another PC, Mac, iPad - literally any device running an SSH client can be used. Default password of user `root` is `flash`. Access is possible through (replace IP with your address)

`
% ssh root@IP 
`
