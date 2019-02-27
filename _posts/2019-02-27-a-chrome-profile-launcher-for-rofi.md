---
layout: post
title: A Chrome profile launcher for Rofi
lang: en
category: code
---

_**What is Rofi?** [Rofi](https://github.com/DaveDavenport/rofi) is a powerful window switcher, application launcher and dmenu replacement for Linux which provides the user with a textual list of options where one or more can be selected. This can either be running an application, selecting a window, or options provided by an external script._

## Dependencies

It only requires `bash` and `python`.

```sh
rofi -modi 'Chrome Profile':/path/to/rofi-chrome-profile-launcher.sh -show 'Chrome Profile'
```

## Usage

Start typing the name of the profile you want to open, then press Enter.

When you launch it:

![Screenshot1](https://github.com/claudiodangelis/rofi-chrome-profile-launcher/blob/master/screenshot-1.png?raw=true)

When you start typing:

![Screenshot2](https://github.com/claudiodangelis/rofi-chrome-profile-launcher/blob/master/screenshot-2.png?raw=true)

## Usage with i3wm


To use it [i3wm](https://i3wm.org) add this line to `.i3/config`.


```
bindsym Mod1+Shift+Control+o exec rofi -modi 'Chrome Profile':/path/to/rofi-chrome-profile-launcher/rofi-chrome-profile-launcher.sh -show 'Chrome Profile'
```
