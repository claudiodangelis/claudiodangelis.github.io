---
layout: post
title: Refactoring Popup For Keep
lang: en
category: code
---

It's been more than 3 years now since I open sourced [Popup For Keep](https://chrome.google.com/webstore/detail/popup-for-keep/fhcmhglnohogibbbpbodmjeggpdlboop), a simple extension to open Google Keep into a Chrome's top-right corner popup.

![Screenshot Of Popup For Keep](/assets/img/posts/popupforkeep.jpg)

<!--more-->

Although it was initially developed to satisfy my own silly whim, it quickly reached **more than 13000 installs** and the number keeps growing, and the number of feature requests I receive from users keeps growing as well.

However, given that it wasn't intended to be adopted by more than 1 user (me), and given the need to fix bugs and add features as the userbase grows, the current code is getting quite a mess, and it definitely needs some refactoring.

The initial version (the current one on the Chrome Web Store) is just a bunch of JavaScript functions, but I'm fixing this. I'm writing the next version with Angular 5 in its TypeScript flavor.


What has been refactored so far:

- account discovery
- loading/saving settings
- showing Google Keep into the popup

What is being refactored:

- "add page/selection to keep" feature
- options page
- accounts switching
- the "detach popup" feature

What I plan to introduce right after refactoring the code:

- make the popup resizeable (this could actually be built as a third-party, reusable library)
- add the "add image to Keep" feature
- add internationalization

I will post updates here from time to time and if you have **feedback or feature requests**, drop me a line by email or twitter, or file an issue on github.

The source code and the issue tracker can be found at [github.com/claudiodangelis/popup-for-keep](https://github.com/claudiodangelis/popup-for-keep).
