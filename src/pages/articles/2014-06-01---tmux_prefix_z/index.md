---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- tmux
Description: ""
Tags:
- tmux
date: "2014-06-01"
title: tmuxで一時的にpaneを最大化する
---

知らんかった。便利

tmux 1.8 以降だと、 `prefix z` で最大化をトグルできるみたい。

[tmux で一時的に pane を最大化する - sorry, uninuplemented:](http://rhysd.hatenablog.com/entry/2013/09/16/003620)

ちなみに、vimでは、以下のような設定で似たようなことも出来るみたい。

```vim
nnoremap so <C-w>_<C-w>|
```

2個目の `<C-w>` と `|` の間には、 `<C-v>` があり。( `<C-v>` を2回入力する)

トグルではないけど。

