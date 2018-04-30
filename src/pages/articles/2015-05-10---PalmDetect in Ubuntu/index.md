---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- ubuntu
Description: ""
Tags:
- ubuntu
- synclient
- palmdetect
- trackpad
date: "2015-05-10T08:57:44+09:00"
title: PalmDetect in Ubuntu
---

MacBook AirにUbuntu 15.04をインストールした。
だいたい使いやすくはなっているのだけど、たまにキーボード入力をしている時にタッチパッドが誤反応してしまう時があった。

そこで、手のひら検出？とかいうのを入れてみることにした。

以下のファイルを作成する。

```sh
$ vim ~/.config/autostart/synclient.desktop
```

内容は以下の通り

```sh
[Desktop Entry]
Type=Application
Exec=synclient PalmDetect=1
Hidden=false
NoDisplay=false
X-GNOME-Autostart-enabled=true
Name[ja]=synclient(PalmDetect)
Name=synclient(PalmDetect)
Comment[ja]=
Comment=
```

Superキーを押して出てくる検索メニューから「自動起動するアプリケーション」を選択して、そこで記載するのと同じ。

- - -

##### 参考
[安全にsynclientでUbuntuのタッチパッド設定を変更する - Qiita](http://qiita.com/kaz8/items/51a43a7290c29919bc2e)

