# Table of Contents

[Installation](#Installation)

[Configuration](#Configuration)
- [USB Keyboard Mapping](#USB-Keyboard-Mapping)
- [raspi-config](#raspi-config)
- [retropie-setup](#RetroPie-Setup)
- [8bitdo NES30 Bluetooth Pairing](#8bitdo-NES30-Bluetooth-Pairing)
- [8bitdo NES30 Button Mapping](#8bitdo-NES30-Button-Mapping
)
- [Xbox One RF Adapter](#Xbox-One-RF-Adapter)
- [config.txt](#config)
- [cmdline.txt](#cmdline)


# Installation

Download latest [retropie](https://retropie.org.uk/download/) and Flash with [balenaEtcher](https://www.balena.io/etcher/)

# Configuration

## USB Keyboard Mapping
Boot into retropie, device should expand filesystem to full sdcard and reboot.

Connect USB keyboard. Press any key to start mapping. Optimal keyboard mapping to match raspiconfig defaults*:

    dpad*    -> arrow keys, respectively
    start     -> H
    select    -> G
    a*        -> Enter/Return
    b*        -> ESC
    hotkey    -> left control or tab, etc

---

## raspi-config
Navigate through RetroPie UI to RetroPie Settings.

Within RetroPie Settings, Navigate to `raspi-config`

1 ) System Options
- I’m running ethernet and wireless is weak on the 3b so I’m skipping Wireless LAN, will later disable in /boot/config.txt
- Audio works by default, no changes
- Password default is *raspberry* (default user pi), change password as needed
- Hostname, no need to change for now: skipping

2 ) Display Options
- D1 | Resolution changed to [DMT 82 1920x1080p @ 60hz]
- D2 | Underscan [enabled]

3 ) Interface Options
- P2 | SSH [enabled], for sftp file transfer: default user pi:raspberry

4 ) Performance Options -> No changes, skip

5 ) Localisation Options 
- L1 | Locale changed to [en_US.UTF-8 UTF-8]
- L2 | Timezone changed to [US/Chicago]
- L3 | Keyboard changed to [Generic 105-key PC (intl.)/US/default/no compose key]

6 ) Advanced Options
- A1 | [Expand filesystem] if not already done by retropie


Finish/Exit and Reboot System

---

## RetroPie Setup

Connect to internet.

Navigate through RetroPie UI to RetroPie Settings.

Within RetroPie Settings, Navigate to `retropie setup`

### I ) Basic Install -> Skip

### U ) Update (packages only)

- Would you like to update the underlying OS packages (eg kerbel etc)? **`No`**

### P ) Manage packages
-  d | driver
    - xpadneo | install
    - xpad | remove (reinstalled later with xow)

    The xpadneo driver disables ertm without having to run any code, fixing several common bluetooth issues

Exit to retropie setup main menu.

`R | Perform reboot`

---

## 8bitdo NES30 Bluetooth Pairing

After reboot, Head back to RetroPie Setup menu

(The previous step involved a package that disables etrm with a driver. If you're having issues with bluetooth, try enabling the xpadneo driver from the previous step, which disables ertm.

Also, I'm aware there is a bluetooth menu, but doing it from the setup allows you do delete a bluetooth device and go back a menu within the same UI, restarting the bluetooth interface quicker for failed bluetooth pairings. 

If your bluetooth connection fails, delete the device and exit bluetooth, then go back into bluetooth and retry.)

### C | Configuration
- bluetooth 
    - M | Configure bluetooth connect mode (currently: background)
    - 8 | 8bitdo mapping hack (ON - old firmware)

    - P | Pair and Connect Devices
        - (Turn on 8bitdo NES30 with R and START held down. 
LED will flash led once/second)
        - Select first pairing mode on retropie, DisplayYesNo
        -  If pin prompt appears, 0000
        - Device should be paired

    - U | Setup udev rule, enable for device

	exit bluetooth

exit Configuration

`R | Perform Reboot` also restarting controller on boot to test bluetooth pairing.

---

## 8bitdo NES30 Button Mapping

Boot/Reboot device and controller, device should auto pair after Emulation Station has started.

If not, 
- Limit other bluetooth devices within the same room.
- Restart the controller within 1 foot of the pi.
- Plug the controller in, if low on power.

When paired, controller will likely have no effective inputs, so you'll have to initiate the mapping with the usb keyboard:

If usb keyboard is mapped as above, hit START which I have mapped to H and select `Configure Input`, then Yes.

Hold any button on the NES30 to start mapping:
- dpad -> dpad
- abxy -> abxy
- LR -> L Shoulder, R Shoulder
- Skip remaining except hotkey, with arrow keys on usb keyboard
- Hotkey -> Select

---

## Xbox One RF Adapter

Reinstall xpad driver in `RetroPie Setup/Manage packages/driver`.

Update system, CTRL+ALT+F4:

```
sudo apt update
sudo apt upgrade -y
```

Clone, Make, and Install [medusalix/xow](https://github.com/medusalix/xow)

Reboot system, mapping contoller like in previous examples.

---

## config

The following was appeneded as new lines to `/boot/config.txt`

[Documentation](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README) for overlays in /boot/config.txt

```
$ sudo nano /boot/config.txt

----- Appended to /boot/config.txt -----

# Disable Wi-Fi
dtoverlay=disable-wifi

# Disable Splash and Ignore Warnings
disable_splash=1
avoid_warnings=1

# Max USB Power
max_usb_current=1

```

The above three sections: disables onboard wifi, disables the rainbow splash and lightning bolt power warnings etc, and raises the USB power from 600 to 1200.

---

## cmdline

The following was appened to the line in `/boot/cmdline.txt`

Not a new line, on the same line:
```
$ sudo nano /boot/cmdline.txt

----- Appended to the same line -----

logo.nologo vt.global_cursor_default=0 
```

change existing console=tty? to `console=tty3`

I forget what it was on before, I think tty1

`logo.nologo` removes the four raspberrys on boot

`vt.global_cursor_default=0` removes the blinking cursor on boot

---

After optimal setup was complete, a backup image of the system was created with [imgclone](https://github.com/tom-2015/imgclone).