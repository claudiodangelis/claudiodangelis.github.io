---
layout: post
title: qr-filetransfer is now called qrcp
lang: en
category: projects
---
![screenshot](/assets/img/posts/qrcp.png)

I just published qrcp, originally called qr-filetransfer, a command line tool to transfer files over wifi from your computer to your mobile device by scanning a QR code without leaving the terminal.


<!--more-->

## Quick startup



Install it

```sh
go get github.com/claudiodangelis/qrcp
```



Send a file!



```sh
qrcp /path/to/file
```

![screenshot](https://raw.githubusercontent.com/claudiodangelis/qrcp/master/demo.gif)

Scan the QR code with your mobile, then transfer the file to your mobile.


It also works the other way around, if you want to transfer files from your mobile to your desktop.

![screenshot](https://raw.githubusercontent.com/claudiodangelis/qrcp/master/mobile-demo.gif)

Use the `receive` command

```sh
qrcp receive 
```

Scan the URL, it brings you to an upload page, choose the files you want to transfer, then upload the files to your desktop.



Check the full README.md and the source code at [github.com/claudiodangelis/qrcp](https://github.com/claudiodangelis/qrcp).

