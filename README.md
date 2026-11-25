# Updating Dremel 3D20 Firmware on Apple Silicon Macs (Parallels + Windows 11 ARM64)

## Overview & Use Case
The **Dremel 3D20** (OEM: Zhejiang FlashForge Dreamer, USB ID `VID_2A89&PID_8889`) requires a Windows-only utility (`dremel_firmware.exe`) to update its firmware to the final release (**V1.8 / 2021-08-04**). Unlike newer Dremel models (3D40/3D45), the 3D20 does not support standard front-panel SD card firmware updates.

When running **Windows 11 on ARM** inside **Parallels Desktop** on an Apple Silicon Mac (M1/M2/M3/M4), the legacy x86/x64 USB drivers bundled with the Dremel DigiLab Slicer fail to load because Windows 11 ARM64 blocks non-ARM kernel drivers. However, because the 3D20 uses a bulk-USB interface compatible with Microsoft's native **WinUSB** driver (`winusb.sys`), you can bind WinUSB directly to `VID_2A89&PID_8889` and flash the firmware cleanly from Parallels.

This driver and procedure may also be useful for direct Windows ARM installations.

---

## Prerequisites
* **Hardware:** Apple Silicon Mac, Dremel 3D20, and a direct USB-B to USB-C data cable (or direct adapter—avoid multi-port USB hubs if possible).
* **Software:**
  * Parallels Desktop running **Windows 11 (ARM64)**.
  * [Dremel 3D20 V1.8 Firmware ZIP] (download from Dremel).  * Either [Zadig](https://akeo.ie) (recommended) **or** the custom `.inf` file included in this repository.

---

## Step 1: Pass the Printer Through to Parallels
1. Power on the Dremel 3D20 and connect it to your Mac via USB.
2. Confirm macOS sees the hardware under **Apple menu () > System Information > USB** (it will appear as a Dremel or FlashForge USB device).
3. Boot your Windows 11 VM in Parallels Desktop.
4. In the top macOS menu bar, click **Devices > USB & Bluetooth** (or **External Devices**) and select the Dremel 3D20 so a checkmark appears next to it.
5. In Windows, open **Device Manager** (`Win + X` > **Device Manager**) and verify that **BOSCH Dremel 3D Printer** (or an unknown USB device with Hardware ID `USB\VID_2A89&PID_8889`) appears under **Other devices**.

---

## Step 2: Bind Microsoft's Native WinUSB Driver

Using the Custom `.inf` File (No Third-Party Tools)
You can install the included `dremel_winusb.inf` file manually:

1. Download or recreate `dremel_winusb.inf` inside `C:\DremelFW\`.
2. Because the `.inf` is unsigned, temporarily disable Driver Signature Enforcement in Windows:
   * Hold **Shift** while clicking **Start > Power > Restart**.
   * Navigate to **Troubleshoot > Advanced options > Startup Settings > Restart**.
   * After rebooting, press **`7`** (or **`F7`**) to select **Disable driver signature enforcement**.
3. Open **Device Manager**, right-click **BOSCH Dremel 3D Printer** under **Other devices**, and select **Update driver > Browse my computer for drivers**.
4. Select `C:\DremelFW\`, click **Next**, and choose **Install this driver software anyway** when prompted. *(Driver signature enforcement re-enables automatically on your next reboot.)*

---

## Step 3: Flash the Firmware
1. Extract the Dremel V1.8 firmware archive to a local folder on the Windows `C:\` drive (e.g., `C:\DremelFW\`)—do not run it from a Parallels shared Mac network folder.
2. Right-click `dremel_firmware.exe` and select **Run as administrator**.
3. Confirm the updater detects the connected 3D20 and start the update.
4. The printer touchscreen will display **Downloading**. Leave the USB cable connected and Parallels active for **1–3 minutes** until the transfer finishes and the printer automatically reboots.
5. On the 3D20 touchscreen, navigate to **Tools > Machine Information** (or **About**) to confirm the firmware now reads **V1.8 (`20210804`)**.
