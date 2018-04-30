---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- tmux
Description: ""
Tags:
- tmux
date: "2013-06-16"
title: tmuxで画面サイズをリセットしてアタッチ
---

tmux で attach する場合、事前に attach されていた
画面がある時、その画面サイズに固定されてしまう。

そのため、 attach する前にオプションで detach させるようにする。

zsh で自動 attach するには、 .zshrc にこんな感じで書いてる。

```sh
if [ -z $TMUX ]; then
  tmux attach -d || tmux
fi
```

##### 参考:

[tmuxでアタッチした時、画面がおかしくなるのを直した \#tmux - Qiita [キータ]](http://qiita.com/items/fa28ae844bc820c2da57)

[Tmuxの設定とメモ](http://filmlang.org/computer/tmux)
