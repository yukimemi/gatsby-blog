---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- mac
Description: ""
Tags:
- mac
- mavericks
date: "2013-10-26"
title: Mac OS X Mavericks クリーンインストール
---

Mavericks がなんと無料で出たので、早速アップデートしました。

新しいOSにする時は毎回全消去してからまっさらな状態で
新規インストールするので、今回も [【Mac】OS X Mavericks (マーベリックス)を クリーンインストールする方法 | 和洋風KAI](http://wayohoo.com/mac/tips/how-to-clean-install-os-x-mavericks.html)
を参考にして新規インストールした。

インストールした後は、以下のステップで環境を構築。

### 1. github と bitbucket にsshキーを登録

```sh
$ ssh-keygen
  Generating public/private rsa key pair.
  Enter file in which to save the key (/Users/yukimemi/.ssh/id_rsa): [Enter]
  ... 全部 [Enter]
  ..
  .
$ cat ~/.ssh/id_rsa.pub | pbcopy
```

github と bitbucket に元々登録してあったsshキーを削除して
から、新たにクリップボードに入ってるキーを登録。

(このsshキーの登録もコマンドから出来たりせんのかな・・・)

### 2. github から、 dotfiles をクローン

自分の環境構築用 dotfiles をクローンする。

```sh
$ git clone git@github.com:yukimemi/dotfiles.git
$ cd dotfiles
$ ./setup.sh
```

これで一応ほぼ環境構築は終わり。
後は各アプリケーションの設定を個別にやるくらい。

setup.sh の中身は以下のような感じ。 中でもとても便利なのが、
homebrew cask 。 これはWindowsの chocolatey
みたいに、アプリケーションを インストールしてくれる。

```sh
#!/bin/bash
git submodule init
git submodule update

# tmux or screen
echo "local? , server?"
echo "l , s"
read lors
while :
do
  if [ "${lors}" = "l" ]; then
    tmux=".tmux.conf"
    break
  elif [ "${lors}" = "s" ]; then
    tmux=".tmux.conf_server"
    break
  else
    echo "Press l , s"
    read lors
  fi
done
# tmux
rm ${HOME}/.tmux.conf
ln -s ${PWD}/${tmux} ${HOME}/.tmux.conf

# zsh
cd zsh
ZSH_FILE=( .zshenv )
for file in ${ZSH_FILE[@]}
do
  rm ${HOME}/${file}
  ln -s ${PWD}/${file} ${HOME}/${file}
done
cd ../

[[ ! -d "${HOME}/.oh-my-zsh" ]] && git clone git@github.com:yukimemi/oh-my-zsh.git ~/.oh-my-zsh
rm ${HOME}/.zshrc
ln -s ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

# vim
cd vim
VIM_FILE=( .vimrc .gvimrc .vim )
for file in ${VIM_FILE[@]}
do
  rm ${HOME}/${file}
  ln -s ${PWD}/${file} ${HOME}/${file}
done
cd ../

[[ ! -d vim/.vim/back ]] && mkdir -p vim/.vim/back

# vimperator
cd vimperator
[[ ! -d ${HOME}/.vimperator/plugin ]] mkdir -p ${HOME}/.vimperator/plugin
git clone git://github.com/caisui/vimperator.git ${HOME}/.vimperator/caisui
git clone git://gist.github.com/377348.git ${HOME}/.vimperator/377348
git clone git://github.com/vimpr/vimperator-plugins.git ${HOME}/.vimperator/vimperator-plugins
git clone git://github.com/vimpr/vimperator-rc.git ${HOME}/.vimperator/vimperator-rc

rm ${HOME}/.vimperatorrc
ln -s ${PWD}/.vimperatorrc ${HOME}/

rm ${HOME}/.vimperator/plugin/plugin_loader.js
ln -s ${HOME}/.vimperator/vimperator-plugins/plugin_loader.js ${HOME}/.vimperator/plugin/
rm ${HOME}/.vimperator/colors
ln -s ${HOME}/.vimperator/vimperator-rc/anekos/colors ${HOME}/.vimperator/
cd ../

# dotfiles
DOT_FILES=( .vrapperrc .gemrc)
for file in ${DOT_FILES[@]}
do
  rm ${HOME}/${file}
  ln -s ${PWD}/${file} ${HOME}/${file}
done

# KeyRemap4MacBook
cd mac
[[ ! -d ${HOME}/Library/Application\ Support/KeyRemap4MacBook ]] && mkdir -p ${HOME}/Library/Application\ Support/KeyRemap4MacBook
rm ${HOME}/Library/Application\ Support/KeyRemap4MacBook/private.xml
ln -s ${PWD}/private.xml ${HOME}/Library/Application\ Support/KeyRemap4MacBook/private.xml
cd ../

# global gitignore
rm ${HOME}/.gitignore
ln -s ${PWD}/global-gitignore ${HOME}/.gitignore
git config --global core.excludesfile ~/.gitignore

# git
git config --global user.name 'yukimemi'
git config --global github.user 'yukimemi'
git config --global push.default simple
git config --global color.diff auto
# alias
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.st status
# editor
git config --global core.editor vim

# install homebrew
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
brew install reattach-to-user-namespace
brew install --disable-etcdir zsh
brew install lv
brew install git
brew install git-now
brew install tmux
brew install readline
brew install openssl
brew install coreutils
brew install rmtrash
brew install cmatrix
brew install zsh-completions
brew install mosh
brew install nkf
brew install rbenv ruby-build rbenv-gemset rbenv-binstubs
brew install macvim --with-cscope --with-lua --HEAD
brew install go
ln -s /usr/local/Cellar/macvim/HEAD/MacVim.app /Applications

brew tap phinze/homebrew-cask
brew install brew-cask
brew cask install google-chrome
brew cask install firefox
brew cask install right-zoom
brew cask install app-cleaner
brew cask install keyremap4macbook
brew cask install caffeine
brew cask install bettertouchtool
brew cask install iterm2
brew cask install quicksilver
brew cask install dropbox
brew cask install evernote

# scripts git clone
[ ! -d ${HOME}/bin ] && mkdir ${HOME}/bin
cd ${HOME}/bin
git clone git@bitbucket.org:yukimemi/scripts.git

# iterm2 solalized colorscheme
git clone https://github.com/altercation/solarized.git ~/.solarized
```

##### 参考:

[OSX - みんなhomebrew-caskって知ってるか？ - Qiita](http://qiita.com/ryurock/items/1432578d364985f6cb06)

