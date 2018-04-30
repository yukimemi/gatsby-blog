---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
- mac
Description: ""
Tags:
- vim
- mac
- single
- apfs
date: "2017-10-01T18:21:37+09:00"
title: Fix conf with single user mode on mac
---

`mac` でシングルユーザーモードの起動と、設定ファイルの修正方法。

nas の自動マウントを行おうとして、 `automount` の設定を変更していたら、再起動後、 `mac` が起動しなくなった。

その修正方法。

[Mac をシングルユーザモードまたは Verbose モードで起動する](https://support.apple.com/ja-jp/HT201573)

ここを見ればわかるが、mac起動時に、 `Command + S` を押しっぱなしでシングルユーザーモードに入れる。

んで、あとは、 `vi` で設定をもとに戻して終了・・・っと思いきや、設定を書き込み出来なかった。

デフォルトだと、 `/` が読み込み専用でマウントされている。
書き込み可能で再マウントする。 (High Sierra だったので、APFS)

```sh
# mount_apfs -uw /
```

これで書き込みできる。
設定は、 man で確認できる。

```sh
# man 8 mount
```

修正後、再起動して完了。

```sh
# reboot
```

- - -

##### 参考

[Mac をシングルユーザモードまたは Verbose モードで起動する](https://support.apple.com/ja-jp/HT201573)

