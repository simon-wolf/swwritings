---
date: 2022-06-06 12:00
title: NixOS and Sway 
categories: linux
---

## Introduction

After much procrastination and playing around with alternatives, I have settled on [NixOS](https://nixos.xxx) and [Sway](xxx) as the Linux distribution and window manager I will be using on my on my new laptop and I will probably be migrating my desktop machine over to both shortly too.

## Motivation and Goals

My laptop is a fairly low specification machine (see my previous post about this) so I wanted to try running something more lightweight than a full-blown desktop environment and ... 

## NixOS

NixOS takes a declarative approach to structuring an operating system. It uses the Nix package manager to build your environment using a script or scripts. You define, for example, the packages you want installed, in a configuration script and then your system is updated accordingly. This allows you to create a reproducible, self-documented system which is great for installing the same setup across multiple machines and for knowing exactly what you have installed at any particular time.   

Having used Ubuntu for a couple of years I have a desktop computer that contains all sort of software. Some of it I use regularly and some of it was installed for one specific purpose and then never removed. I could go through it all and clean things up but it is not something I really want to face. With NixOS I can read a configuration file and know exactly what I have installed and, thanks to comments, why.

As an added bonus, if I need some software for a specific purpose, whether it is temporary or for a software development project, then I can define individual environments which install and make the software packages available when the environments are running but keep the rest of my machine free of them when they are not.

## Sway

Sway is a Wayland window manager. Let's unpack that...

When you use a computer you are probably using a desktop environment. For some people it will be Windows, for others it will be macOS. On Linux there are multiple desktop environments (GNOME, KDE, etc.) but they all need a display server which is what manages what is shown on screen. Historically this has been X11 but an alternative, Wayland, is slowly gaining traction.

Wayland was designed to be more secure than X11, mainly because each application is treated as a 'client' to the video hardware's 'server'. The display server no longer needs to manage everything at once, rather it manages each client as and when needed. This also helps improve performance which is ideal for a computer with limited hardware.

If you use Windows or macOS or GNOME or KDE on Linux then you are using a desktop environment. The base set of application installed and the look-and-feel of everything is managed for you. Things like an application launcher, the taskbar, etc. are all included as are things like how you configure wireless networks, manage then screens brightness, etc. Application and document windows will all have a style (title bar, maximise and minimise buttons, etc.) and windows can be dragged around and positioned where you want or displayed full-screen.

A window manager is much more bare-bones. It is in charge of positioning, sizing and moving application windows. And that is it. No applications are installed with them and no theming is applied. Everything you do with a window manager is up to you. Essentially you are taking the window management part of a desktop environment and nothing else (and so, yes, a desktop environment includes a window manager).


## Waybar


## Tips & Tricks

### Backlight

I'm using the '[light](https://haikarainen.github.io/light/)` utility to control the screen brightness. It does not use X (so works in Wayland) but it does need some priviledges.

The [NixOS wiki](https://nixos.wiki/wiki/Backlight) explains that the `configuration.nix` file needs to include:

```
programs.light.enable = true;
```

It also warns that if you turn the backlight off completely you will be unable to see what you are typing so I wrote a small script to prevent this.

In the Sway config:

```
bindsym XF86MonBrightnessDown exec $HOME/.config/sway/scripts/lower_brightness.sh
```

And the script itself is simply:

```bash
!/bin/sh

change_by=5
min=30

brightness_float=$(light)
brightness=${brightness_float%.*}
new_brightness=`expr $brightness - $change_by`

if [ "$new_brightness" -lt "$min" ]; then
  light -S "$min"
else
  light -U "$change_by"
fi
```

This defines a minimum brightness value and then fetches the current setting, converts it to an integer and, if it is greater than the minimum lowers the brightness.
