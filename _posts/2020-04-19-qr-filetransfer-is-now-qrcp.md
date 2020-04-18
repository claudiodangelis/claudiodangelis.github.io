---
layout: post
title: qrcp, transfer files over wifi from your computer to your mobile device by scanning a QR code without leaving the terminal
lang: en
category: projects
---

I just published qrcp, originally called qr-filetransfer, a command line tool to transfer files over wifi from your computer to your mobile device by scanning a QR code without leaving the terminal.

![screenshot](assets/img/posts/qrcp.png)

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

![screenshot](https://raw.githubusercontent.com/claudiodangelis/qr-filetransfer/qrcp/demo.gif)

3. Scan the QR code with your mobile

4. Download the file to your mobile



It also works the other way around, if you want to transfer files from your mobile to your desktop.

![screenshot](https://raw.githubusercontent.com/claudiodangelis/qr-filetransfer/qrcp/mobile-demo.gif)

1. Use the `receive` command

```sh
qrcp receive 
```

2. Scan the URL, it brings you to an upload page

3. Choose the files you want to transfer

4. Upload the files to your desktop



Check the full README.md and the source code at [github.com/claudiodangelis/qrcp](https://github.com/claudiodangelis/qrcp).

