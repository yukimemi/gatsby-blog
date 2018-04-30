---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- ubuntu
Description: ""
Tags:
- ubuntu
- mayu
- mac
date: "2015-05-02T09:23:30+09:00"
title: Install Ubuntu 15.04 to MacBook Air (11-inch, Late 2010)
---

使ってるMacBook Airが最近遅くて耐えられなくなってきたので、Ubuntuに乗り換えてみた。

nvidiaのドライバ関係でいろいろ大変だったのでメモ。

### Install Usbメモリの作成
```sh
$ sudo dd if=./ubuntu-15.04-desktop-amd64.iso of=/dev/sdb bs=4M
```

### Ubuntu インストール
nvidiaのグラフィックドライバをインストールする場合、grubがefiモードじゃなくてbiosモードでインストールされる必要があるっぽい。
そこで、通常とはちょっと違う方法でインストールする。

まず、最初の選択で、 "Try Ubuntu without installing it" を選ぶ。
その後、 "Ctrl + Alt + T" を押して端末を起動し、インストーラを起動する。

```sh
$ sudo ubiquity
```

それから、 "それ以外" ってのを選んで、自分でパーティションを作成する。
必要なパーティションは以下の3つ

* 予約されたBIOSブート領域: 1M
* ext4(/): swap以外全部
* swap: 2048M

その後はそのままインストールを続ける。
全部終わると再起動が促されるが、そのまま再起動せずに、端末を起動し、grubをインストールする。

```sh
$ sudo mount /dev/sda2 /mnt # rootパーティション
$ sudo grub-install --root-directory=/mnt /dev/sda
$ sudo grub-install --root-directory=/mnt --recheck /dev/sda

$ sudo mount --bind /dev /mnt/dev
$ sudo mount --bind /dev/pts /mnt/dev/pts
$ sudo mount --bind /proc /mnt/proc
$ sudo mount --bind /sys /mnt/sys
$ sudo chroot /mnt
$ update-grub
$ exit
```

usbをさしたままだと、sdbにはインストールできないとかなんとかのエラーが出るけど、むし。
コマンドが全部終わったら再起動してusbを抜く。

普通にubuntuが起動するのでログインし、システム設定より、ソフトウェアとアップデートを開き、追加のドライバータブより、NVIDIAのドライバーにチェックを入れて適用する。
ここのバージョンは変わるっぽいけど、現時点では、「NVIDIA binary driver -version 340.76をnvidia-340から使用します(プロプライエタリ、検証済み)」ってやつだった。

あと、Wi-Fiのドライバは、使用しない方が安定してた。使用すると、スリープ復帰後にWi-Fiが切断される。

プロプライエタリのドライバをインストール後は、以下のコマンドを実行して、xorg.confファイルを作成する。

```sh
sudo nvidia-xconfig
```

バックライトの調整ができるように、xorg.confファイルに1行追加する。

```sh
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "GeForce 320M"
    Option         "RegistryDwords" "EnableBrightnessControl=1" # この行
EndSection
```

終わったら再起動して完了。

あとは細かい設定をしていくだけ。
インストールして使えるようになるのはここまで。
デフォルト状態でもかなり使える。ubuntuすごい。

- - -

##### 参考
[MacBook Air Late 2010 on Ubuntu 14.04 (※05/30追記)](http://variedtastefinder.jpn.ph/diary/395/)

[\[SOLVED\] Installing Ubuntu from USB on a MacBook Air 3,1-3,2 (nvidia card)](http://www.ubuntuforums.org/showthread.php?t=2209602)


