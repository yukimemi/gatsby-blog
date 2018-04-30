---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- powershell
Description: ""
Tags:
- powershell
- vim
- vim-plugin
date: "2013-10-13"
title: powershell vim plugin
---

powershell script を vim で書くためのプラグイン

```vim
NeoBundleLazy 'PProvost/vim-ps1', {
      \   'autoload' : {'filetypes': ['ps1']}
      \ }
```
