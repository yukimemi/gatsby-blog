---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- git
Description: ""
Tags:
- git
- github
- deploy
date: "2014-07-02"
title: Git directory deploy !
---

roots のデプロイ手順。

僕の環境では、まず drafts ブランチで書いて、 master ブランチでpushして
githubへ更新という流れ。
その際、 roots では、ビルド結果を public というディレクトリに出力するので
そのディレクトリだけをpushしたい。
gitで管理している中の、ある一部のディレクトリのみをpushしたい場合は、 `git subtree push` という
のが使えるらしいんだけど、参考にしたサイトでは違うアプローチをとっているみたい。

[X1011/git-directory-deploy](https://github.com/X1011/git-directory-deploy)

rootsに限らず、静的サイトジェネレーターを使う場合はけっこう使えると思う。

### [これ](https://github.com/X1011/git-directory-deploy) をダウンロード

```sh
$ wget https://github.com/X1011/git-directory-deploy/raw/master/deploy.sh && chmod +x deploy.sh
```

### git-directory-deployの設定
以下の部分を必要に応じて書き換え。

```sh
deploy_directory=public
deploy_branch=master

#if no user identity is already set in the current git environment, use this:
default_username=yukimemi
default_email=yukimemi@gmail.com

#repository to deploy to. must be readable and writable.
repo=origin
```

### 初回一度だけ実行

```sh
$ git --work-tree public checkout --orphan master
$ git --work-tree public rm -rf "*"
$ git --work-tree public add --all
$ git --work-tree public commit -m "initial publish"
$ git push -f origin public
$ git symbolic-ref HEAD refs/heads/drafts && git reset --mixed
```

### デプロイの仕方
まずは、 drafts ブランチですべてコミットしておく。(未コミットが残っているとエラーになる。)
それから、 roots でコンパイル。
あとは、ダウンロードした deploy.sh を実行するだけ。

```sh
$ roots compile --no-compress
$ ./deploy.sh
```

べんり。

---

##### 参考:

[X1011/git-directory-deploy](https://github.com/X1011/git-directory-deploy)

