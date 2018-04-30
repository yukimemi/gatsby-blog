---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- solarized
- colorscheme
date: "2016-09-19T11:15:50+09:00"
title: Use solarized colorscheme in true color
---

前回の記事([True color in iTerm2 Tmux Neovim | YUKIMEMI](http://yukimemi.github.io/post/2016-09-19_True%20color%20in%20iTerm2%20Tmux%20Neovim/))で、 `iterm` と `neovim` でtrue colorを使う方法はわかったんだけど、なぜか、 `solarized` の `colorscheme` が有効にならなかった。


通常の `solarized` ではダメなようで、true color対応したやつを使えばいいみたい。

```toml
[[plugins]]
repo = 'lifepillar/vim-solarized8'
```

```vim
colorscheme solarized8_dark
```

これでできた。

- - -
##### 参考

[tmux on iTerm2 で斜体 & True Color を使う - Qiita](http://qiita.com/delphinus35/items/b8c1a8d3af9bbacb85b8)

