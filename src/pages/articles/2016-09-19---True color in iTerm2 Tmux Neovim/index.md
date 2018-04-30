---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
- tmux
- iterm
Description: ""
Tags:
- vim
- tmux
- iterm
date: "2016-09-19T00:30:00+09:00"
title: True color in iTerm2 Tmux Neovim
---

今時 `macbook` のキレイな画面で256色なんて・・・

っということで、 `True color` が使えるそうなので設定してみた。

まず、 `iTerm2` の設定。

は特になく、 `homebrew cask` でインストールすればそのまま `true color` が使えるらしい。

```sh
$ brew cask install iterm2
```

次に `tmux` の設定。
`tmux` はバージョン `2.2` 以上ならOKっぽい。普通に `homebrew` でインストールしたら `2.2` だった。

```sh
$ brew install tmux
```

で、設定ファイル `tmux.conf` に以下を追加。

```sh
set -ga terminal-overrides ",xterm-256color:Tc"
```

もし、以下の設定が書いてあったらコメントアウト。
```sh
# set -g default-terminal xterm-256color
```

それから、 `neovim` の設定 ( `init.vim` )。

```vim
if has('nvim')
  set termguicolors
endif
```

これでOK。
昔の設定は、
`let $NVIM_TUI_ENABLE_TRUE_COLOR=1`
とかだったらしいけど、2016/5/11から変わったっぽい。

`terminal` で `true color` なのすごいべんり。
`iTerm2` の `colorscheme` に関係せずに `vim` の `colorscheme` を変えられる。

設定方法を書いてくれた人たちに感謝。

- - -
##### 参考

[tmux on iTerm2 で斜体 & True Color を使う - Qiita](http://qiita.com/delphinus35/items/b8c1a8d3af9bbacb85b8)

[True HEX Colors with Neovim and iTerm2 - Prevent Default](http://sts10.github.io/blog/2015/10/24/true-hex-colors-with-neovim-and-iterm2/)


