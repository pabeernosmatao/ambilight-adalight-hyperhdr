# HyperHDR Setup & Boot Fix for CachyOS / Arch Linux

This guide provides a comprehensive manual to download the latest HyperHDR binary release, integrate it directly into your KDE Plasma Application Launcher, and permanently fix the USB serial port (`ttyUSB0`) detection issue on CachyOS / Arch Linux.


## 🛠️ Section 1: Downloading & Deploying HyperHDR

Since CachyOS runs on Arch Linux, we download the official pre-compiled Linux binary package from the [HyperHDR Releases Page](https://github.com/awawa-dev/HyperHDR/releases).

1. Go to the official repository downloads and grab the latest `.tar.gz` package for Linux architecture (typically x86\_64).

2. Extract the archive into your home directory under a clean structure:

```
mkdir -p ~/HyperHDR  
tar -xvf HyperHDR-\*.tar.gz -C ~/HyperHDR --strip-components=1
```

*Note: Ensure the main executable is located exactly at `~/HyperHDR/bin/hyperhdr` as shown in your launcher setup.*


## 🖥️ Section 2: Adding HyperHDR to KDE Plasma Launcher

To launch HyperHDR elegantly from your applications menu without annoying file manager prompts, create an official desktop application entry.

1. Right-click your Application Launcher button (Start Menu) on the Plasma taskbar and select **"Edit Applications..."** (Editor del menú de KDE).

2. Select a target category (e.g., *Juegos* or *Utilidades*) and click **"+ New / Nuevo"** -\> **"New Item"**.

3. Fill out the **General** profile precisely matching these fields:

   - **Name / Nombre:** `LedsHDR`

   - **Program / Programa:** `/home/YOUR\_USER/HyperHDR/bin/hyperhdr` *(Replace `YOUR\_USER` with your actual Linux username)*

4. Click **"Save / Guardar"** in the top right corner.

*The application will now appear naturally in your system menu with an assigned icon launcher, bypassing any temporary desktop prompt limits.*


## ⚡ Section 3: Automatic USB Ambilight Detection Fix

### The Problem

Hardware serial ports for LED controllers (Arduino, ESP32, CH340 chipsets) register natively under the `uucp` group on Arch. Systemd boot scheduling triggers a race condition where the USB port sets up boundaries before user profile permissions fully settle inside the active desktop. This causes a detection failure on boot until you either hot-plug the cable or execute manual `usermod` group updates.

### The Permanent Fix

We bypass rigid group dependencies entirely by applying systemd's dynamic `uaccess` hardware seat tags. This maps complete communication access directly to whichever user owns the current graphical seat.

#### Step 1: Force Base Group Membership

Map your current user profile directly to the legacy communication grid:

```
sudo usermod -aG uucp $USER
```

#### Step 2: Disable Braille Screen Interferences

The native accessibility service (`brltty`) aggressively hooks USB-Serial communication devices during cold boots, locking HyperHDR out. Permanently disable and mask it:

```
sudo systemctl stop brltty-udev.service  
sudo systemctl mask brltty-udev.service  
sudo systemctl stop brltty.service  
sudo systemctl disable brltty.service
```

#### Step 3: Write the Dynamic udev Permissive Rule

Generate a dedicated udev file to catch any serial connection (`ttyUSB0`, `ttyUSB1`, etc.), adjust standard boundaries (`0666`), and tag it for local session management:

```
sudo echo 'KERNEL=="ttyUSB\[0-9\]\*", SUBSYSTEM=="tty", TAG+="uaccess", MODE="0666"' | sudo tee /etc/udev/rules.d/99-leds.rules
```

*(If your controller chip translates as an ACM device instead, change `ttyUSB\[0-9\]\*` to `ttyACM\[0-9\]\*`).*

#### Step 4: Refresh System Device Engine

Instruct the running kernel to ingest the new layout adjustments instantly:

```
sudo udevadm control --reload-rules && sudo udevadm trigger
```

#### Step 5: Perform a System Reboot

```
sudo reboot
```

After restarting your computer, fire up your new `LedsHDR` shortcut from the Plasma application selection. HyperHDR will capture your `ttyUSB0` connection instantly on every boot.

