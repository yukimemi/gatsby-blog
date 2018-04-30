---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- font
Description: ""
Tags:
- font
- ricty
- homebrew
date: "2013-09-14"
title: Ricty Mac HomebrewでRicty Powerline フォントをインストールする
---

最近動作が重くなってきたこともあり、Mountain Lion を
再インストールした。

再インストールは簡単で、起動時に 「 Command + R 」を押せばOK。

まずディスクユーティリティでディスクを消去して、Mountain Lion
の再インストール。

再インストール後、 iTerm2 や、 MacVim のフォント Ricty
のインストールと Powerline
のパッチ当てをまた簡単に出来るようにスクリプトとしてまとめた。

```sh
#!/bin/sh

brew tap sanemat/font
brew install ricty

cmd=$(brew info ricty | grep "Ricty\*.ttf" | sed -e "s/.*\(cp -f.*\)/\1/")
echo $cmd
eval $cmd

#git clone https://github.com/Lokaltog/powerline.git ~/.powerline
#fontforge -script $HOME/.powerline/font/fontpatcher.py ~/Library/Fonts/Ricty-Regular.ttf

git clone https://github.com/Lokaltog/vim-powerline ~/.vim-powerline
fontforge -lang=py -script ~/.vim-powerline/fontpatcher/fontpatcher ~/Library/Fonts/Ricty-Regular.ttf

mv -f *.ttf ~/Library/Fonts/

# wait a minute
fc-cache -vf
  ```

Powerline の方のパッチではなぜかうまく出来なかったので、
vim-powerline の方で行っている。

##### 参考:

[brew install ricty |
實松アウトプット](http://sanematsu.wordpress.com/2013/05/11/brew-install-ricty/)

[Vim - powerlineをいつ使う？今でしょ！ - Qiita [キータ]](http://qiita.com/alpaca_taichou/items/ab70f914a6a577e25d70)

[【Linux Mint, vim】vim-powerlineにフォントRictyのパッチをあてる - i'll be fine](http://calorie001.hatenablog.com/entry/2012/11/08/054056)

