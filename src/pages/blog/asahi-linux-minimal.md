---
layout: ../../layout/MarkdownLayout.astro
title: Asahi Linux Minimal install on Apple Silicon
description: "Installing Asahi Linux Minimal on Apple Silicon and configuring the Sway window manager for Wayland."
pubDate: 2023-10-18
tags: ["Linux"]
---

# Installing Asahi Linux Minimal on Apple Silicon.

Asahi Linux is a project and community with the goal of porting Linux to Apple Silicon Macs. You can start the installation process by pasting the following command into a terminal:

```bash
curl https://alx.sh | sh
```

Or visit the [Asahi Linux website](https://asahilinux.org/) for more detailed instructions.


> ⚠️ Read through each step carefully. Whilst Asahi Linux is easy to setup, if you make a mistake, you might send your Mac into a bootloop and may have to reformat the disk.

### Login

After installing Asahi Linux Minimal you'll be booted into a black screen with nothing but a login prompt. The [Arch Linux Wiki](https://wiki.archlinux.org/) has great documentation on the installation process and I recommend reading through it, however not all of the steps are necessary as some of it has been handled by the Asahi Linux installation process already.

The first thing you're going to want to do is login, you can do that using the username and password below:

```shell
username: root
password: root
```

### Connecting to the internet

If you have an ethernet connection you can skip this step. If you're on a Macbook however, you're probably going to want to setup wifi.
To do this we're going to use **iwd**. The first step is to start the service using the commands:

```shell
systemctl start iwd.service
iwctl
```

Once inside the **iwd** prompt the following commands will help us find our device and scan for nearby networks.

```shell
device list
station WLAN get-networks
station WLAN connect SSID
```

Replace **"WLAN"** with the name of your device and **"SSID"** with the name of your network.

After that you can exit the iwd prompt with **Ctrl+D** and then finally run:

```shell
dhcpcd
```

### Updating Pacman

The next thing we need to do is update the system, but before that theirs some tweaks we can do to **pacman.conf** to make this a bit quicker.

```bash
nano /etc/pacman.conf
```
Now uncomment the following line:

```bash
#ParallelDownloads = 5
```
You can also set the number to whatever you'd like depending on your connection and hardward, I normally bump this up to around 10 - 15.

And finally run an update with `pacman -Syu`.

### Post installation / ArchDI

We're finally ready to start setting up the system for use. The easiest way to do this, in my opinion, is to use [ArchDI](https://github.com/MatMoul/archdi). Get it by running:

```bash
curl -L archdi.sf.net/archdi > archdi
sh archdi
```

Simply go through each section of the user interface and choose which packages you want to install, which users to create and more. Refer back to the [Arch Wiki](https://wiki.archlinux.org/title/General_recommendations) for a list of reccomended packages.

### Setting up a tiling window manager

Finally I'm going to install a tiling window manager, as I prefer these to traditional desktop environments. My window manager of choice is [river](https://github.com/riverwm/river), but to get it running we're first going to need the Asahi Linux GPU drivers.

```bash
sudo pacman -S linux-asahi-edge mesa-asahi-edge
sudo update-grub
```
Then reboot and install your window manager (and bar if you need it) of choice.
```bash
sudo pacman -S river waybar
```

After that simply login from your display manager and you're ready to start using Asahi Linux!

![image](/images/asahilinux.webp)

## Thoughts on Asahi Linux

Overall my experience on Asahi Linux has been a positive one. There are some caveats you need to be aware of though, the speakers, the keyboard backlight and the dynamic notch are all not working right now (Asahi crops the aspect ratio to 16:9 to avoid the notch area), but in my opinion these are minor issues.

The bigger issues lie within the ARM64 platform, specifically with the lack of packages available. One of the biggest issues I encountered whilst using Asahi Linux was the lack of DRM content support. This is because there is no AArch64 build of **Widevine**, which is the proprietary digital rights management (DRM) technology from Google used by the Chromium and Firefox web browsers. I often like to have Spotify open whilst I code, and since their is no client available on AArch64 either, the web browser is your only option (and it doesn't work).

**EDIT:** I did actually find a workaround using **spotify-tui** and **spotifyd**, but the lack of DRM support will still effect many other services like Amazon Prime, Netflix, etc.

I also could not compile a **Wayland** version of **emacs** on AArch64 either, the native version runs through **XWayland** which can look blurry depending on the level of scaling on your display (and you will need some level of scaling if you're using a Macbook display).

The work of the Asahi Linux team is incredible and I'm very happy a project like this exists. I can only hope support for the AArch64 platform improves by the time Asahi Linux is finished. Of course, it is already plenty useable, but it really depends on your needs, for me to be able to recommend it to everyone, it's going to need more time.