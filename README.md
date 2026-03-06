![Sketch](/images/sgipi_gh.jpg)

[![YouTube Channel Views](https://img.shields.io/youtube/channel/views/UCz5BOU9J9pB_O0B8-rDjCWQ?style=flat&logo=youtube&logoColor=red&labelColor=white&color=ffed53)](https://www.youtube.com/channel/UCz5BOU9J9pB_O0B8-rDjCWQ) [![Github](https://img.shields.io/github/stars/veebch?style=flat&logo=github&logoColor=black&labelColor=white&color=ffed53)](https://www.github.com/veebch/)

# SGI Pi

The lack of modularity on the Pi 500+ was enough to make us build an alternative desktop solution out of a Compute Module 5. The idea being to make something a bit more future proof that doesn't involve a perfectly good keyboard becoming ewaste once the brains of the machine start to look dated. 

It's has a footprint that is about ten times smaller and is a few hundred times more powerful than the original. It runs RaspiOS.

It has the upside of letting you use a hot-swappable keyboard so that you can swap out switches as you go down the mechanical keyboard rabbit hole. It also has full size HDMI ports and a speaker to let you add a [startup sound](https://www.youtube.com/watch?v=CH9saUP2460).

# Parts
- A Raspberry Pi CM5
- A Raspberry Pi CM5 IO Board
- nVME drive
- An 8 ohm 3w speaker
- MAX98357 Audio Power Amplifier Module
- 3d printed case (stl files in [/3d](/3d))
- 4 x feet (to give some clearance for the down-firing speaker)
- Wires (to attach the speaker to the HAT)
- microSD card ≥8 GB (for RaspiOS transfer)

# Assembly

Attach the 8 ohm speaker to the 3d printed lid, pointing downwards. Then connect to the MAX98357 amp as follows:

| MAX98357A Pin | Connect to CM5 J8 Pin | GPIO   | Function                |
| ------------- | --------------------- | ------ | ----------------------- |
| VIN           | Pin 2 or Pin 4        | —      | 5 V power supply        |
| GND           | Pin 6 (or any GND)    | —      | Ground                  |
| BCLK          | Pin 12                | GPIO18 | I²S Bit Clock           |
| LRC           | Pin 35                | GPIO19 | I²S Word Select (LRCLK) |
| DIN           | Pin 40                | GPIO21 | I²S Audio Data          |

| MAX98357A | Speaker   |
| --------- | --------- |
| SPK+      | Speaker + |
| SPK−      | Speaker − |

Attach the CM5 io board to the top of the enclosure.
Place the rubber feet on the second (bottom part) of the enclosure, to give the speaker some room. Slide the cover on. Screw shut (TODO: Add images)

# Putting RaspiOS on the NVMe

## Prerequisites
- CM5 mounted on IO Board  
- NVMe SSD in USB/PCIe adapter  
- microSD card ≥8 GB (for first boot)  
- Monitor, keyboard, and mouse for desktop setup  
- A **second computer** to flash the SD card and/or NVMe

---

## Step 1: Prepare microSD on a second computer
1. Download and install [Raspberry Pi Imager](https://www.raspberrypi.com/software/) on another computer.  
2. Insert your SD card.  
3. Select OS -> **Raspberry Pi OS Full Desktop (Trixie)**.  
4. Select the SD card as target.  
5. (Optional) Open **Advanced Options** (gear icon) to set hostname, enable SSH, configure Wi-Fi, and locale.  
6. Click **Write** and wait for completion.  

> **Note:** This initial flashing must be done on a separate computer before first boot on the CM5.

---

## Step 2: Boot CM5 from SD card
- Insert SD card into the IO Board.  
- Connect **monitor, keyboard, and mouse**.  
- Power on - the desktop environment should appear.  
- Open a terminal and update the system:

```bash
sudo apt update && sudo apt full-upgrade -y
sudo rpi-update
sudo reboot
```

---

## Step 3: Update EEPROM
```bash
sudo rpi-eeprom-update
sudo rpi-eeprom-update -a
```

---

## Step 4: Set boot order for NVMe
```bash
sudo rpi-eeprom-config --edit
```

Set:

```ini
BOOT_UART=1
BOOT_ORDER=0xf461  # SD first, NVMe second, USB third
```

Save and exit.

---

## Step 5: Prepare NVMe
- Insert NVMe SSD via USB/PCIe adapter.  
- Verify it is detected:

```bash
lsblk
```

- SD card: `/dev/mmcblk0`  
- NVMe: `/dev/nvme0n1`  

- Wipe NVMe if necessary:

```bash
sudo wipefs -a /dev/nvme0n1
```

---

## Step 6: Flash NVMe with Raspberry Pi Imager (optional on second computer)
1. On a **second computer** or on the CM5, open **Raspberry Pi Imager**.  
2. Select OS -> **Raspberry Pi OS Lite (Trixie)**.  
3. Select NVMe SSD as target.  
4. (Optional) Configure Advanced Options for SSH, Wi-Fi, and locale.  
5. Click **Write** and wait for completion.

> Alternatively, if using CM5 CLI, you can use `rpi-imager --cli <image> /dev/nvme0n1`.

---

## Step 7: Copy configuration for headless boot (optional)
If you want preconfigured settings on NVMe, mount the NVMe boot partition:

```bash
sudo mkdir /mnt/nvfat
sudo mount /dev/nvme0n1p1 /mnt/nvfat

sudo cp /boot/firmware/user-data /mnt/nvfat/
sudo cp /boot/firmware/network-config /mnt/nvfat/
sudo cp /boot/firmware/config.txt /mnt/nvfat/
```

Optionally edit `/mnt/nvfat/cmdline.txt` to suppress first-boot prompts:

```
quiet splash plymouth.ignore-serial-consoles cfg80211.ieee80211_regdom=US
```

---

## Step 8: Boot from NVMe
1. Shut down CM5.  
2. Remove SD card.  
3. Power on CM5 - it should now boot from NVMe and display the full desktop.

---

## Step 9: Post-install updates
```bash
sudo apt update && sudo apt full-upgrade -y
sudo rpi-update
sudo reboot
```

- Optional: expand filesystem via `sudo raspi-config` -> “Expand Filesystem”  
- Configure Wi-Fi, user accounts, and desktop applications as desired.



# Final Steps

In your running RaspiOS, edit `/etc/firmware/config.txt` to include

```
dtparam=i2s=on
dtoverlay=hifiberry-dac
```

reboot, and then test with:

```speaker-test -c1 -twav -D hw:0,0```




















