---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- mac
Description: ""
Tags:
- mac
- type
- tickeys
date: "2016-09-03T10:56:35+09:00"
title: Type sound with Tickeys
---

最近、
[Emacsで効果音(SE)を付けて作業効率を3倍にする方法](http://rubikitch.com/2016/08/17/sound-wav/)
という記事を見つけた。

やっぱ音があった方がやる気が出るし、コードもすらすら書けるのではないか？
僕は形から入るタイプなのである・・・。

そこで、Emacsと限らず他のものでもすべて音が出るようなソフトはないかと探してみた。

[yingDev/Tickeys](https://github.com/yingDev/Tickeys)

`Rust` で書かれてるらしい。

インストール方法は homebrew cask で一発。

```sh
$ brew cask install tickeys
```

あとはシステム環境設定のアクセシビリティから許可するだけ。

起動したら `QAZ123` とタイプすることで設定画面が開く。

音を選択出来るので、 `Cherry G80-3000` ってやつにした。
良い音！たくさんタイプしたくなるので、コード書くのが捗ること間違いなし。

- - -
##### 参考

[Tickeys – メカニカルキーボードのタイプ音を堪能できるMacアプリ](http://www.softantenna.com/wp/mac/tickeys/)

