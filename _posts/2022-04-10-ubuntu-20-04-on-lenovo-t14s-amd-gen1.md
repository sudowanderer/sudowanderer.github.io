---
layout: post
title: Ubuntu 20.04 on Lenovo T14s AMD Gen1
date: 2022-04-10 19:26 +0800
img_path: /assets/
categories: Linux
tags: Ubuntu
---

Basically, all machine functions working well, including:

* Intel AX200 wireless card
* HDMI output with audio
* DP output on USB-C port (one cable, including power supply, video and audio output)
* USB hub on USB-C port
* wake up from sleep

All above functions tested in `Ubuntu 20.04.4 LTS` with kernel  `5.13.0-39-generic`


```shell
mike@ThinkPad-T14s:~$ uname -a
Linux ThinkPad-T14s 5.13.0-39-generic #44~20.04.1-Ubuntu SMP Thu Mar 24 16:43:35 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

### Requirements

#### Bootable USB drive

1. download latest image from  [Ubuntu official website](https://ubuntu.com/download/desktop).
2. use [Rufus](https://rufus.ie/en/)  burn ISO to your `USB` Drive

#### BIOS Settings

Before migrate from `Windows` to `Ubuntu`,  you need change some `BIOS` settings to make `Ubuntu` behave normally.

**how to enter `BIOS`**:

1.  press `enter` on the Lenovo screen when booted.
2.  press `F12` enter the `BIOS`.

**Settings**:

* set “OS Optimized Defaults” to “Disabled”.

![image-20220313115835171](T14sUbuntu.assets/image-20220313115835171.png)

* disable secure boot

  <img src="T14sUbuntu.assets/secure_boot2.jpg" style="zoom:67%;"  alt="secure boot"/>

* set sleep state to `Linux`( this fix can not wake up issues)

  config->power->sleep state

  <img src="T14sUbuntu.assets/lenovo-bios-targus_1024x1024.jpg" style="zoom:67%;" alt=" lenovo bios"/>

#### Installment

**boot from USB drive**:

1.  press `enter` on the Lenovo screen when booted.
2.  press `F12` enter the boot source selection screen.
3.  chose your `USB` drive

**third-party driver**:

The opensource third-party driver for `AMD Ryzen 7 PRO` series chips runs really well on `Thinkpad T14s`, remember check the box on install wizard.

![](T14sUbuntu.assets/2-3.png)

**update system**:

After install, update system to latest.

```shell
sudo apt update && sudo apt upgrade
```

### Optimization

Below sections are optional optimizations for `Ubuntu`. Make your own choice.

Tips: If you install `Ubuntu` with the latest ISO image from [Ubuntu official website](https://ubuntu.com/download/desktop), you don't  need install the OEM kernel manully. [switch to Linux oem kernel](#switch-to-linux-oem-kernel).

#### switch to Linux oem kernel

After you install `Ubuntu`, default kernel is generic. It may have some annoying problems, like, you can not size brightness with hotkeys, and display colors are not accurate compare to windows. Recommend switch to Linux oem kernel, all above problems will gone.

more details about OEM Kernels, see below link:

[OEM Kernel](https://wiki.ubuntu.com/Kernel/OEMKernel)

use this command to install:

```shell
sudo apt install linux-oem-20.04b
```

#### set mouse wheel speed

Ubuntu default mouse speed is too slow.We need use a config to make it better.

1. install imwheel

   ```shell
   sudo apt install imwheel
   ```

2. create `.imwheelrc`

   ```shell
   sudo gedit ~/.imwheelrc
   ```

   content:

   ```shell
   ".*"
   None,      Up,   Button4, 3
   None,      Down, Button5, 3
   Control_L, Up,   Control_L|Button4
   Control_L, Down, Control_L|Button5
   Shift_L,   Up,   Shift_L|Button4
   Shift_L,   Down, Shift_L|Button5
   ```

   change the number 3 more smaller if you think the speed is too fast.

3. starting onboot

   use command `imwheel -kill` to start imwheel. Also, you could use `gnome-session-properties` set this command as a startup command.

#### Change Ubuntu notification position to right

1. install  gnome tweaks and gnome-shell-extensions

   ```shell
   sudo apt install gnome-tweaks gnome-shell-extensions
   ```

2. install Host Connector ()

   ```shell
   sudo apt install chrome-gnome-shell
   ```

3. use chrome open https://extensions.gnome.org/, install `Notification Banner Position`

#### Show the net speed on the status bar

1. make sure you already installed `gnome-tweak`, `gnome-shell-extensions`
2. search  `Net speed Simplified ` in the extensions store.

![image-20220410155456298](T14sUbuntu.assets/image-20220410155456298.png)

### Software recommendation

For using Ubuntu more conveniently , I recommend some nice software on `Ubuntu`.

#### Onedrive

If you just migrate from `Windows`, and still have some important files on `onedrive`, you should use this opensource [onedrive client](https://github.com/abraunegg/onedrive) for Ubuntu(works in terminal).

you can use the install script from `github`, or install from a custom apt repository.

1. add repository and install

   ```shell
   sudo add-apt-repository ppa:yann1ck/onedrive && sudo apt install -y onedrive
   ```

2. login

   ```shell
   onedrive
   ```

3. sync with specific directory

   ```shell
   onedrive --synchronize --single-directory [dirName]
   ```

#### flameshot

Best sceenshot tools on linux.

```shell
sudo apt install flameshot
```

#### Remmina

Best remote desktop app in Linux, support `RDP` ,`VNC` protocals. If you have another windows machine which is often accessed, use this app to access your windows machine on `Ubuntu`.

```shell
sudo snap install remmina
```
