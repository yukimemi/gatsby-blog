---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- golang
Description: ""
Tags:
- golang
- peco
- zsh
- ghq
date: "2014-06-29"
title: All You Need Is Peco
---

最近 [peco](https://github.com/peco/peco) がかなり流行りっぽい。
そこで導入してみた。思ったより使い心地よくて、 [zaw](https://github.com/zsh-users/zaw) から乗り換えた。

---

### 導入手順

homebrewでgoをインストール。
```sh
$ brew install go --cross-compile-common
```

`.zshenv` に以下を記載。
```sh
# go
export GOPATH=$HOME/.go
export PATH=$GOPATH/bin:$PATH

[ ! -d $GOPATH ] && mkdir $GOPATH
if which go > /dev/null; then
    export GOROOT=$(go env GOROOT)

    # ghq
    go get github.com/motemen/ghq
    git config --global ghq.root ~/.ghq
    # peco
    go get github.com/peco/peco/cmd/peco
    # gh-open
    go get github.com/typester/gh-open

fi
```

`.zshenv` を読み込み直す
```sh
$ source ~/.zshenv
```

インストール完了。
```sh
$ ls -l | peco
QUERY>                                                         IgnoreCase [1/1]
total 96100
drwxr-xr-x  13 yukimemi staff      442  6 27 05:07 Applications
drwx------+  3 yukimemi staff      102  6 23 22:00 Desktop
drwx------+  6 yukimemi staff      204  5 25 10:58 Documents
drwx------+ 20 yukimemi staff      680  6 22 08:20 Downloads
drwx------  94 yukimemi staff     3196  6 29 00:23 Dropbox
drwx------+ 47 yukimemi staff     1598  5 18 12:26 Library
drwx------+  3 yukimemi staff      102  5 17 18:13 Movies
drwx------+  4 yukimemi staff      136  5 18 00:35 Music
drwx------+  3 yukimemi staff      102  6  8 18:24 Pictures
drwxr-xr-x+  5 yukimemi staff      170  5 17 18:13 Public
drwxr-xr-x   5 yukimemi staff      170  6 29 00:07 bin
drwxr-xr-x  35 yukimemi staff     1190  6 28 17:20 dotfiles
```

### **peco** 使い方

#### history
以下を `.zshrc` に記載
```sh
function peco-select-history() {
  local tac
  if which tac > /dev/null; then
      tac="tac"
  else
      tac="tail -r"
  fi
  BUFFER=$(history -n 1 | \
      eval $tac | \
      peco --query "$LBUFFER")
  CURSOR=$#BUFFER
  zle clear-screen
}
zle -N peco-select-history
bindkey '^r' peco-select-history
```

`<C-r>` で起動。こんな感じ。

```sh
QUERY>                                                           IgnoreCase [1/189]
ls   -l | peco
cd
go env
env G -i go
pvim
pwd
git co drafts; roots watch
mux github
exit
pn
```

#### resent dirs
以下を `.zshrc` に記載
```sh
zstyle ':filter-select' case-insensitive yes
autoload -Uz chpwd_recent_dirs cdr add-zsh-hook
add-zsh-hook chpwd chpwd_recent_dirs
zstyle ':chpwd:*' recent-dirs-max 5000
zstyle ':chpwd:*' recent-dirs-default yes
zstyle ':completion:*' recent-dirs-insert both

function peco-cdr () {
    local selected_dir=$(cdr -l | awk '{ print $2 }' | peco)
    if [ -n "$selected_dir" ]; then
        BUFFER="cd ${selected_dir}"
        zle accept-line
    fi
    zle clear-screen
}
zle -N peco-cdr
bindkey '^@' peco-cdr
```

`<C-@>` で起動。こんな感じ。

```sh
QUERY>                                                           IgnoreCase [1/4]
~
~/.ghq
~/.ghq/bitbucket.org/yukimemi/haskell
~/.ghq/github.com/yukimemi/yukimemi.github.io
~/.cabal/bin
~/.ghq/bitbucket.org/yukimemi/yukimemi.bitbucket.org
~/dotfiles
~/dotfiles/.tmuxinator
~/Documents/git/yukimemi.github.io
~/.ghq/github.com/zcbenz/nw-sample-apps
~/.go
~/Pictures
~/Downloads
```

#### snippets
以下を `.zshrc` に記載
```sh
function peco-snippets() {
    BUFFER=$(grep -v "^#" ~/.peco-snippets | peco --query "$LBUFFER")
    zle clear-screen
}
zle -N peco-snippets
bindkey '^s' peco-snippets
```

`~/.peco-snippets` によく使うコマンドを記載しておく。
```sh
# find
find . -type f -mtime +10
find . -mtime +50 -exec mv {} del \;

# date
date +%Y%m%d

# kill
kill -9 $(ps -ef | grep -i vlc | grep -v grep | awk '{ print $2 }')
kill -9 $(ps -ef | grep -i firefox | grep -v grep | awk '{ print $2 }')

# screensaver
open /System/Library/Frameworks/ScreenSaver.framework/Resources/ScreenSaverEngine.app

# exiftool
exiftool '-FileName < CreateDate' -d ~/backup/Photos/%Y-%m/%Y-%m-%d_%H-%M-%S%%-c.%%e *.(JPG|jpg)
exiftool '-FileName < CreateDate' -d ~/backup/Mov/%Y-%m/%Y-%m-%d_%H-%M-%S%%-c.%%e *.(MOV|mov)

# neobundle
vim +NeoBundleInstall! +q

# ghq
# github
ghq get git@github.com:yukimemi/md2docx.git
ghq get git@github.com:yukimemi/gitstatus.git

# bitbucket
ghq get git@bitbucket.org:yukimemi/haskell.git
ghq get git@bitbucket.org:yukimemi/node-webkit.git
ghq get git@bitbucket.org:yukimemi/clojure.git
ghq get git@bitbucket.org:yukimemi/go.git
ghq get git@bitbucket.org:yukimemi/scripts.git
```

`<C-s>` で起動。こんな感じ。
```sh
QUERY>                                                           IgnoreCase [1/1]
find . -type f -mtime +10
find . -mtime +50 -exec mv {} del \;

date +%Y%m%d

kill -9 $(ps -ef | grep -i vlc | grep -v grep | awk '{ print $2 }')
kill -9 $(ps -ef | grep -i firefox | grep -v grep | awk '{ print $2 }')

open /System/Library/Frameworks/ScreenSaver.framework/Resources/ScreenSaverEngine.app

exiftool '-FileName < CreateDate' -d ~/backup/Photos/%Y-%m/%Y-%m-%d_%H-%M-%S%%-c.%%e *.(JPG|jpg)
exiftool '-FileName < CreateDate' -d ~/backup/Mov/%Y-%m/%Y-%m-%d_%H-%M-%S%%-c.%%e *.(MOV|mov)

vim +NeoBundleInstall! +q

ghq get git@github.com:yukimemi/md2docx.git
ghq get git@github.com:yukimemi/gitstatus.git

ghq get git@bitbucket.org:yukimemi/haskell.git
ghq get git@bitbucket.org:yukimemi/node-webkit.git
ghq get git@bitbucket.org:yukimemi/clojure.git
ghq get git@bitbucket.org:yukimemi/go.git
ghq get git@bitbucket.org:yukimemi/scripts.git
```

#### ghq
最近 [peco](https://github.com/peco/peco) も流行りだけど、 [ghq](https://github.com/motemen/ghq) も流行り。ちょうべんり。
上記で記載済みの導入手順に従っていればインストールされているはず。

以下を `.zshrc` に記載
```sh
alias ghl='cd $(ghq list -p | peco)'
alias gho='gh-open $(ghq list -p | peco)'
```
これで、 ghq のどれかに `cd` したり、該当のgithubをブラウザで開いたり出来る。

#### vim
以下を `.zshrc` に記載
```sh
alias pvim='vim "$(find . -type f | peco)"'
```
これで、カレントディレクトリ以下のファイルをすぐにvimで開ける。ちょうべんり。

いろんな参考ページの方々ありがとうございます。

---

##### 参考:
[Big Sky :: Windows のコマンドプロンプトを10倍便利にするコマンド「peco」](http://mattn.kaoriya.net/software/peco.htm)

[pecoを使い始めた - $shibayu36->blog;](http://shibayu36.hatenablog.com/entry/2014/06/27/223538)

[peco、ghq、gh-openの組み合わせが捗る - Webtech Walker](http://webtech-walker.com/archive/2014/06/peco-ghq-gh-open.html)

[peco/percolでCUIなスニペットツールを作ってみる - Glide Note - グライドノート](http://blog.glidenote.com/blog/2014/06/26/snippets-peco-percol/)


