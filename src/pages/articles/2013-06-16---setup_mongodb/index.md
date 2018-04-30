---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- mongodb
Description: ""
Tags:
- mongodb
- mac
date: "2013-06-16"
title: Mac OSX Mountain Lion MongoDB セットアップ
---

mongodb をいれてみた。

```sh
$ brew install mongodb
```

これでインストール完了。簡単。

自動起動する設定も書いてあるからそれを実行するだけ。

```sh
$ ln -sfv /usr/local/opt/mongodb/*.plist ~/Library/LaunchAgents
$ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
```

これで自動起動される。

手動で起動するには、次のようにする。

```sh
$ launchctl start homebrew.mxcl.mongodb
```

