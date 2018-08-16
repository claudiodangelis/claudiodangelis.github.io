---
title: News from qr-filetransfer development
layout: post
category: code
---

<img src="https://raw.githubusercontent.com/claudiodangelis/qr-filetransfer/master/logo.svg?sanitize=true" alt="qr-filetransfer logo"/>

Two important news from [qr-filetransfer](https://github.com/claudiodangelis/qr-filetransfer) development, a project I launched a few months back:

1. Ability to transfer files from mobile to desktop
2. As you might have guessed, we have a logo!


<!--more-->
##  Mobile-to-Desktop transfers

To transfer files from mobile to desktop, you need to pass the `-receive` flag followed by the path you want to transfer files to:

```sh
qr-filetransfer -receive ~/Downloads
```


By scanning the QR you will be prompted with an upload page on your mobile:


![Screenshot](https://github.com/claudiodangelis/qr-filetransfer/raw/master/mobile-demo.gif)


## The "quick-fingers" logo

About the logo, it's called **quick-fingers** and it was provided by [@arasatasaygin](https://github.com/arasatasaygin) as part of the [openlogos](https://github.com/arasatasaygin/openlogos) initiative, a collection of free logos for open source projects. 

Check out the rules to claim one: [rules of openlogos](https://github.com/arasatasaygin/openlogos#rules).



---

Relevant Github threads:

- Initial PR, now superseded https://github.com/claudiodangelis/qr-filetransfer/pull/8
- Conversation https://github.com/claudiodangelis/qr-filetransfer/issues/58
- Final PR https://github.com/claudiodangelis/qr-filetransfer/pull/74


Thanks again to the amazing Github community!
