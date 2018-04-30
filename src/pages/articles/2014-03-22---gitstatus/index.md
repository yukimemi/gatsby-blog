---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- golang
Description: ""
Tags:
- golang
- git
date: "2014-03-22"
title: golang git 管理のすべてのディレクトリで 「git status」する
---

Go言語が流行りっぽいので、Goを使って、カレントディレクトリ配下の、gitで管理されているすべてのディレクトリで「git
status」を実行するプログラムを書いてみた。
別にGoで書く必要はないんだけど・・・。

[yukimemi/gitstatus](https://github.com/yukimemi/gitstatus)

```sh
$ gitstatus yukimemi
```

みたいにすると、「gitstatus」を実行したディレクトリ配下で、「git remote
-v」に「yukimemi」が含まれるものを対象に、「git
status」を実行して出力する。引数を指定しなければ、すべてのディレクトリで。

出力イメージはこんな感じ。

```sh
root /Users/yukimemi
--------------------------------------------------------------------------------
★  /Users/yukimemi/.oh-my-zsh
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   templates/zshrc.zsh-template

no changes added to commit (use "git add" and/or "git commit -a")

--------------------------------------------------------------------------------

--------------------------------------------------------------------------------
★  /Users/yukimemi/Documents/git/go
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    gitstatus/gitstatus.go

no changes added to commit (use "git add" and/or "git commit -a")

--------------------------------------------------------------------------------

--------------------------------------------------------------------------------
★  /Users/yukimemi/Documents/git/yukimemi.bitbucket.org
On branch drafts
Your branch is ahead of 'origin/drafts' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   2014/03/22/gitstatus.rst
        modified:   blog/doctrees/2014/03/22/gitstatus.doctree
        modified:   blog/doctrees/environment.pickle

no changes added to commit (use "git add" and/or "git commit -a")

--------------------------------------------------------------------------------
```

それから、Goはクロスコンパイルが簡単ということで、「drone.io」にてクロスコンパイルするようにしてみた。

[Downloads | gitstatus](https://drone.io/github.com/yukimemi/gitstatus/files)

非常に便利。

##### 参考:

[Go言語のビルド生活を drone.ioで幸せに暮らす \#golang - Qiita](http://qiita.com/atotto/items/b796c31c1755dbec13db)

[filepath - The Go Programming Language](http://golang.org/pkg/path/filepath/#Walk)

