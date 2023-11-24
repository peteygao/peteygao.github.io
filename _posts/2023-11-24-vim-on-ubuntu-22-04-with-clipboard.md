---
layout: post
title:  "VIM on Ubuntu 22.04 with clipboard support using Linux Brew"
date:   2023-11-24 15:03:55 -0800
tags: [VIM, Linux Brew, Ubuntu, Clipboard]
---

The prodigal son returns to Linux after years of developing on macOS (and Windows with WSL), it's a longer post to talk about the reasons for the switch to Linux (WSL wasn't the greatest of experience for me, and you know what they say about once bitten twice shy; ergo no WSL2 for me). macOS in comparison to Windows was quite a breeze, and using `brew install vim` works like a charm with clipboard support.

But let's set the stage: you install VIM on Ubuntu 22.04, open up a file and try to yank to the clipboard with `"+y` or `"*y`, and when you hit `Ctrl+V` in a different app, nothing appears.

You then go back to the terminal and look for VIM's `+clipboard` support, but sadly when you run `vim --version`, it shows `-clipboard`.

## Time to fix VIM with `+clipboard`
1. Modify the brew formula, but we need to make a copy first: `cp /home/linuxbrew/.linuxbrew/opt/vim/.brew/vim.rb ./vim.rb`
2. Modify the file: `vim ./vim.rb`. Replace `"--without-x"` with `"--with-x"`.
2. Uninstall the currently installed VIM version: `brew uninstall vim`
3. Install Ubuntu dependencies: `sudo apt-get install libncurses5-dev libgtk2.0-dev libatk1.0-dev libx11-dev libxt-dev`
4. Install VIM from the local formula: `brew install --build-from-source ./vim.rb`

Take a look after it's compiled and installed: `vim --version`.

Voila, now we got our `+clipboard`!
