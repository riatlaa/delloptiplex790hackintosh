<h1 align="center"> Dell Optiplex 790 Hackintosh </h1>

### Specification:

CPU: i5 2400

GPU: GTX 750

Motherboard: Dell 0D28YY

RAM: 8GB DDR3 1333MHz

MacOS Version: 10.13.6 (Higher versions do not support this specification)

OpenCore Version: 0.6.5

## Download configs

https://github.com/riatlaa/delloptiplex790hackintosh/releases

## Required:

- USB Stick 8GB
- Linux (I have not tested on Windows and macOS)
- Internet connection

# Making the installer in Linux
## 1. Download OpenCore 0.6.5
```sh
wget https://github.com/acidanthera/OpenCorePkg/releases/download/0.6.5/OpenCore-0.6.5-RELEASE.zip && unzip OpenCore-0.6.5-RELEASE.zip
```
```sh
# Adjust below command to the correct folder
cd ~/Downloads/OpenCore-0/Utilities/macrecovery/
```

Next, run one of the following commands depending on the OS you'd like to boot:
```sh
python ./macrecovery.py -b Mac-BE088AF8C5EB4FA2 -m 00000000000J80300 download
```

## 2. Create bootable USB

In terminal:

  1) run `lsblk` and determine your USB device block
  <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-5.0cee0d3a.png" alt="lsblk">
  
  2) run `sudo gdisk /dev/<your USB block`
  
     1) if you're asked what partition table to use, select GPT.
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-6.c65407b2.png" alt="gdisk1">
     
     2) send `p` to print your block's partitions (and verify it's the one needed)
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-13.2dca606f.png" alt="gdisk2">
  
     3) send `o` to clear the partition table and make a new GPT one (if not empty)
     - confirm with `y`
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-8.21d65816.png" alt="clear">
     
     4) send `n`
     - partition number: keep blank for default
     - first sector: keep blank for default
     - last sector: `+200M` to create a 200MB partition that will be named later on OPENCORE
     - Hex code or GUID: `0700` for Microsoft basic data partition type
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-15.4ec42b10.png" alt="firstpart">
     
     5) send `n`
     - partition number: keep blank for default
     - first sector: keep blank for default
     - last sector: keep black for default (or you can make it `+3G` if you want to partition further the rest of the USB)
     - Hex code or GUID: `af00` for Apple HFS/HFS+ partition type
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-16.a34cfc74.png" alt="secondpart">
     
     6) send `w`
     - Confirm with `y`
     
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-17.d7a601da.png" alt="writepart">
     
     - In some cases a reboot is needed, but rarely, if you want to be sure, reboot your computer. You can also try re-plugging your USB key.
     
     7) Close `gdisk` by sending `q` (normally it should quit on its own)
  
  3) Use `lsblk` again to determine the 200MB drive and the other partition
  <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-18.4d8a1388.png" alt="lsblk1">
  
  4) run `sudo mkfs.vfat -F 32 -n "OPENCORE" /dev/<your 200MB partition block>` to format the 200MB partition to FAT32, named OPENCORE
  
  5) then `cd` to `/OpenCore/Utilities/macrecovery/` and you should get to a .dmg and .chunklist files
     
     1) mount your USB partition with `udisksctl` (`udisksctl mount -b /dev/<your USB partition block>`, no sudo required in most cases) or with `mount` (`sudo mount /dev/<your USB partition block> /where/your/mount/stuff`, sudo is required)
     
     2) `cd` to your USB drive and `mkdir com.apple.recovery.boot` in the root of your FAT32 USB partition

     3) download `dmg2img` (available on most distros)
     
     4) run `dmg2img -l BaseSystem.dmg` and determine which partition has `disk image` property
     <img src="https://dortania.github.io/OpenCore-Install-Guide/assets/img/unknown-20.7e0e5028.png" alt="dmg2img">
     
     5) run `dmg2img -p <the partition number> -i BaseSystem.dmg -o <your 3GB+ partition block>` to extract and write the recovery image to the partition disk
     - It will take some time. A LOT if you're using a slow USB (took me about less than 5 minutes with a fast USB2.0 drive).
     
Source: https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html#making-the-installer

## 3. Install and Update MacOS High Sierra

  After installing macOS, install the Nvidia Web Driver from https://images.nvidia.com/mac/pkg/387/WebDriver-387.10.10.10.40.140.pkg (MacOS version required: 17G14042)
  
  
### Optional Kexts

- HoRNDIS.kext - USB Tethering from Android
  Download: https://github.com/jwise/HoRNDIS/releases/
