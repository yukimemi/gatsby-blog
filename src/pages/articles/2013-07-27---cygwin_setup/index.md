---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- cygwin
Description: ""
Tags:
- cygwin
- install
date: "2013-07-27"
title: cygwin インストールコマンド
---

Windows では泣く泣く cygwin を使用する。

cygwin のインストールは setup.exe というインストーラーがあり、
必要なパッケージは手動でダウンロードしなきゃいけない。

なんとか簡単にインストール出来ないかと探っていたところ、 cygwin
のコマンドラインオプションがあることを発見。

以下のバッチスクリプトを作っておくと簡単にインストールできる。

```dos
setup-x86.exe -q -d -D -L -s http://ftp.jaist.ac.jp/pub/cygwin/ -l "%USERPROFILE%\.cygwin" -R c:\cygwin -P gcc,git,git-completion,git-svn,make,vim,wget,ruby,screen,zsh,subversion
```

それぞれのオプションの意味は以下の通り

```dos
Command Line Options:
 -A --disable-buggy-antivirus           Disable known or suspected buggy antivirus software packages during execution.
 -C --categories                        Specify entire categories to install
 -D --download                          Download from internet
 -d --no-desktop                        Disable creation of desktop shortcut
 -h --help                              print help
 -K --pubkey                            URL of extra public key file (gpg format)
 -L --local-install                     Install from local directory
 -l --local-package-dir                 Local package directory
 -n --no-shortcuts                      Disable creation of desktop and start menu shortcuts
 -N --no-startmenu                      Disable creation of start menu shortcut
 -O --only-site                         Ignore all sites except for -s
 -P --packages                          Specify packages to install
 -p --proxy                             HTTP/FTP proxy (host:port)
 -q --quiet-mode                        Unattended setup mode
 -r --no-replaceonreboot                Disable replacing in-use files on next reboot.
 -R --root                              Root installation directory
 -S --sexpr-pubkey                      Extra public key in s-expr format
 -s --site                              Download site
 -U --keep-untrusted-keys               Use untrusted keys and retain all
 -u --untrusted-keys                    Use untrusted keys from last-extrakeys
 -X --no-verify                         Don't verify setup.ini signatures
```

このインストールの後に、 apt-cyg を入れれば完璧

```sh
$ svn --force export http://apt-cyg.googlecode.com/svn/trunk/ /bin/
$ chmod +x /bin/apt-cyg
```

##### 参考:

[ばりとんひろば: 【Windows】Cygwin Terminal(mintty) + zshで快適Git](http://dd0125.blogspot.jp/2013/02/windows-cygwin-terminalmintty-zsh-git.html)

[Cygwin FAQ](http://cygwin.com/faq/faq.html#faq.setup.cli)

[apt-cyg - A command-line software installer for Cygwin - Google Project Hosting](http://code.google.com/p/apt-cyg/)
