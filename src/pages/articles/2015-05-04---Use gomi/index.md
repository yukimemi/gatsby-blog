---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- golang
Description: ""
Tags:
- golang
- gomi
- rm
date: "2015-05-04T17:14:11+09:00"
title: Use gomi instead of rm
---

rmを安全に行うツールとして、Macでは「rmtrash」、Linuxでは「trash-cli」を使ってきたけど、最近Golangで「gomi」というツールが作成されたとのことなので使用してみた。
とても便利。

インストールはgo get。
```sh
$ go get -u github.com/b4b4r07/gomi
```

さっそくaliasした。

```sh
$ alias rm='gomi'
```

