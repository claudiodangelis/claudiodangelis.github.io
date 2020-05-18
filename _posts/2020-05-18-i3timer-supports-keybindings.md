---
layout: post
title: i3timer now supports key bindings
lang: en
category: projects
---

I've just pushed an update to [i3-timer](https://github.com/claudiodangelis/i3-timer), a simple timer for the i3 window manager's status bar built for i3blocks, that adds the support to key binding. You can now easily start and stop the timer from keyboard shortcuts.

[!Screenshot](https://raw.githubusercontent.com/claudiodangelis/i3-timer/master/screenshot.png)

<!--more-->

You can setup key bindings to start and stop the timer by using i3blocks' [signaling](https://github.com/vivien/i3blocks#signal) feature. What you should do is to create 2 additional blocks with no `interval` property but set the proper `signal` value for the blocks, then set i3 configuration to bind the emission of the given signals to a keyboard shortcut.

The following example will bind the start of timer to **Mod1+Shift+Control+k** (which will emit, for example, signal `10`) and the stop to **Mod1+Shift+Control+l** (which will emit, for example, signal `11`). After emitting signal `10` or `11`, both key shortcuts will emit signal `12` that will refresh the timer's "gui".


1. Create the main block to configure the timer in i3blocks configuration file:

    ```ini
    [i3timer]
    command=~/go/bin/i3-timer -alarm-command="notify-send 'i3-timer' 'Alarm Elapsed!'; play /usr/share/sounds/freedesktop/stereo/alarm-clock-elapsed.oga"
    interval=10
    signal=12
    ```

2. Create the "start timer" block  in i3blocks configuration file

    ```ini
    [i3timer]
    command=~/go/bin/i3-timer -exec-start
    signal=10
    ```

3. Create the "stop timer" block  in i3blocks configuration file

    ```ini
    [i3timer]
    command=~/go/bin/i3-timer -exec-stop
    signal=11
    ```

4. Create the keyboard shortcut binding in i3 configuration file
    ```ini
    # start timer
    bindsym --release Mod1+Shift+Control+k exec bash -c "pkill -SIGRTMIN+10 i3blocks && pkill -SIGRTMIN+12 i3blocks"
    # stop timer
    bindsym --release Mod1+Shift+Control+l exec bash -c "pkill -SIGRTMIN+11 i3blocks && pkill -SIGRTMIN+12 i3blocks"
    ```

5. Restart i3 and enjoy

