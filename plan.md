
# Ubuntu Dual-Boot Setup Plan

Target machine: ABS Cyclone Aqua Prebuilt Gaming PC  
Current OS: Windows 11  
CPU: Intel Core i7-14700F  
GPU: NVIDIA GeForce RTX 5060 Ti 16GB  
Memory: 16GB DDR5  
Storage: 1TB NVMe SSD  
Goal: Dual boot Windows 11 and Ubuntu

## 0. New PC Sanity Check Before Installing Linux

Because Newegg has a 15-day return window, first verify that the PC works normally in its stock Windows 11 state.

Do this before resizing partitions, changing BIOS storage mode, or installing Ubuntu.

### First Boot / Windows Setup

1. Connect monitor, keyboard, mouse, Ethernet if available, and power.
2. Boot the PC normally.
3. Confirm Windows reaches the initial setup screen.
4. Complete Windows setup:
   - Choose region/language
   - Connect to Wi-Fi, or Ethernet if available
   - Create or sign into a Windows user account
   - Confirm keyboard/mouse/display behave normally

If the system fails to boot, crashes repeatedly, has no display, or cannot complete setup, stop and investigate before modifying anything.

### Confirm Listed Specs

Check that the hardware matches the Newegg listing.

Expected:

- CPU: Intel Core i7-14700F
- GPU: NVIDIA GeForce RTX 5060 Ti 16GB
- RAM: 16GB DDR5
- Storage: 1TB NVMe SSD
- OS: Windows 11

Useful Windows checks:

- `Settings → System → About`
  - CPU
  - Installed RAM
  - Windows edition

- `Task Manager → Performance`
  - CPU model
  - Memory size and speed
  - Disk size/type
  - GPU model and VRAM

- `Device Manager`
  - Display adapters
  - Network adapters
  - Storage controllers
  - Look for warning icons

- `Disk Management`
  - Confirm the internal drive is roughly 1TB
  - Confirm Windows partitions look normal

- `DirectX Diagnostic Tool`
  - Press `Win + R`
  - Run `dxdiag`
  - Check CPU, RAM, display/GPU

Optional command-line checks:

```powershell
systeminfo
wmic cpu get name
wmic memorychip get capacity,speed,manufacturer
wmic diskdrive get model,size
```

Check Networking

Test both, if available:

* Wi-Fi connects successfully
* Ethernet works, if you have a cable handy

Download a file or run Windows Update briefly to confirm the network is stable.

Check GPU Health

Open Task Manager:

Task Manager → Performance → GPU

Confirm the NVIDIA GPU appears.

Optional:

Install/update NVIDIA driver through Windows Update or NVIDIA’s app, then verify the GPU still appears normally.

Do not spend hours tuning Windows if the plan is Ubuntu, but do confirm the GPU is present and not obviously broken.

Check Thermals / Fans / Weird Behavior

During the first 30-60 minutes, watch for:

* Sudden shutdowns
* Random reboots
* Very loud or grinding fans
* Burning smell
* Display flicker/artifacts
* USB ports not working
* Wi-Fi/Bluetooth missing
* Case damage or loose parts

A little fan noise under load is normal. Grinding, rattling, or repeated crashes are not.

Check Windows Activation

Go to:

Settings → System → Activation

Confirm Windows says it is activated.

Even if you mostly plan to use Ubuntu, activation working is part of verifying the product arrived correctly.

1. Back Up Windows First

Before touching partitions, back up anything important from Windows.

At minimum:

* Personal files
* Browser passwords/bookmarks
* Any license keys or app configs
* Recovery keys, especially BitLocker recovery key if BitLocker is enabled

Optional but nice:

* Create a Windows recovery USB
* Confirm you can log into your Microsoft account

2. Check BitLocker Status

In Windows, check whether BitLocker or Device Encryption is enabled.

Go to:

Settings → Privacy & security → Device encryption

or search:

BitLocker

If BitLocker is enabled, disable or suspend it before installing Ubuntu. Ubuntu may not be able to safely resize or install alongside an encrypted Windows partition.

Reference:

https://ubuntu.com/desktop/docs/en/latest/reference/bitlocker-during-ubuntu-installation/

3. Check For Intel RST / RAID / VMD Gotcha

Some Windows PCs ship with Intel RST, RAID, or VMD storage mode enabled instead of plain AHCI.

Ubuntu may not see the NVMe drive correctly if Intel RST/VMD is enabled.

Before installing:

1. Boot into BIOS/UEFI.
2. Look for storage settings such as:
    * Intel RST
    * RAID
    * VMD Controller
    * AHCI
3. If Ubuntu installer cannot see the SSD, this is a likely cause.

Important:

Do not casually flip RAID/RST/VMD to AHCI without preparing Windows first, because Windows may fail to boot afterward. Follow Ubuntu’s guidance for switching Windows to AHCI safely.

Reference:

https://ubuntu.com/desktop/docs/en/latest/reference/intel-rst-during-ubuntu-installation/

4. Resize Windows From Inside Windows

Recommended approach: shrink the Windows partition from Windows before booting the Ubuntu installer.

In Windows:

1. Open Disk Management.
2. Find the main C: partition on the 1TB NVMe SSD.
3. Right-click C:.
4. Choose Shrink Volume.
5. Decide how much space to give Ubuntu.

Suggested split for a 1TB drive:

* Windows: 500-700 GB
* Ubuntu: 250-400 GB

For local LLM / Docker / ML experiments, Ubuntu will appreciate more space. Give Ubuntu at least 300 GB, maybe 400 GB if this box is mostly for Linux tinkering.

After shrinking, leave the new space as Unallocated.

Do not format it in Windows.

5. Download Ubuntu ISO

Download Ubuntu Desktop from:

https://ubuntu.com/download/desktop

Prefer the current LTS release unless you specifically want newer kernel/package behavior.

6. Create Bootable USB With Rufus

On Windows:

1. Download Rufus:
    https://rufus.ie/
2. Insert an 8GB or larger USB stick.
3. Open Rufus.
4. Select the Ubuntu ISO.
5. Use defaults unless needed.
6. For a modern PC, use:
    * Partition scheme: GPT
    * Target system: UEFI
7. Start writing the USB.

Warning:

Rufus will erase the USB stick.

Ubuntu’s guide also recommends Rufus for creating the installer from Windows:

https://ubuntu.com/desktop/docs/en/latest/how-to/create-a-bootable-usb-stick/

7. Boot From The Ubuntu USB

Restart the PC and enter the boot menu.

Common boot menu keys:

* F12
* F11
* Esc
* F8

Pick the USB device, ideally the UEFI entry.

8. Try Ubuntu First

Before installing, choose:

Try Ubuntu

Check basics:

* Display works
* Keyboard/mouse work
* Wi-Fi or Ethernet works
* Ubuntu can see the internal NVMe SSD
* NVIDIA GPU is detected or at least the desktop runs normally

If Ubuntu cannot see the internal SSD, revisit the Intel RST / VMD / RAID setting.

9. Start The Ubuntu Installer

From the live desktop, launch:

Install Ubuntu

When asked about third-party software/drivers, enable:

* Install third-party software for graphics and Wi-Fi hardware
* Download updates while installing, if network is available

This matters because the machine has an NVIDIA GPU.

10. Choose Disk Setup

Preferred option:

Install Ubuntu alongside Windows Boot Manager

If that appears, use it.

If it does not appear:

1. Choose manual partitioning.
2. Select the unallocated space created earlier.
3. Create an Ubuntu root partition:
    * Mount point: /
    * Filesystem: ext4
    * Size: most or all of the unallocated space
4. Use the existing EFI System Partition.
    * Do not format it unless you are very sure.
    * Ubuntu should add its bootloader entry there.

For a simple single-user desktop, you do not need a separate /home partition unless you specifically want one.

11. Finish Install And Reboot

Complete the install.

When prompted:

1. Remove the USB stick.
2. Reboot.
3. You should see a boot menu allowing Ubuntu or Windows.

If it boots straight into Windows, go into BIOS/UEFI boot order and move Ubuntu above Windows Boot Manager, or use the motherboard boot menu.

12. First Ubuntu Boot Tasks

After booting Ubuntu:
```
sudo apt update
sudo apt upgrade
```

Then check NVIDIA drivers:

```
nvidia-smi
```
If nvidia-smi works, the NVIDIA driver is installed and talking to the GPU.

If not, open:

Software & Updates → Additional Drivers

and install the recommended NVIDIA proprietary driver.

13. Sanity Checks After Dual Boot

Confirm both operating systems boot:

* Reboot into Ubuntu
* Reboot into Windows
* Reboot into Ubuntu again

In Ubuntu, check:
```
lsblk
df -h
nvidia-smi
```

In Windows, confirm:

* Windows still boots
* BitLocker/Device Encryption status is what you expect
* Time/date are correct
* Wi-Fi/Ethernet still works

14. Optional Cleanup

Once everything works:

* Keep the Ubuntu USB for recovery
* Write down the BIOS/boot-menu key
* Save BitLocker recovery key if using BitLocker
* Consider making a fresh backup of both systems


