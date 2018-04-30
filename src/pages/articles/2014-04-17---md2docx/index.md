---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- powershell
Description: ""
Tags:
- powershell
- markdown
- word
- docx
date: "2014-04-17"
title: PowerShell Markdown を Wordに変換する
---

仕事でwordを使うことが多々あるのだが、正直いってwordは使いづらい！
勝手にレイアウトが変わったりスタイルが変わったり・・・思い通りにならないこと山のごとし。

なんとかならんもんか！とGoogle先生に聞いたところ、Markdownを使ってdocxを作成出来るものがあるじゃないですか。

[Pandoc - About pandoc](http://johnmacfarlane.net/pandoc/)

ただ、pandocは基本htmlやlatex向きに作成されているのか、wordで思い通りのレイアウトには出来なかった。
特にスタイルとか・・・。

そこで、Markdownからdocxに変換するスクリプトを書いた。

[yukimemi/md2docx](https://github.com/yukimemi/md2docx)

詳しくは README
に記載してあるが、見出しやリストや画像や改ページ等には対応しています。

簡単に試すなら、上記リポジトリにある、 md2docx.cmd と sample.md
をダウンロードし、コマンドプロンプトより、

```bat
> md2docx.cmd sample.md
```

と実行すれば、あら不思議。sample.docxが出来ているはずです。

Windows標準のPowerShellで書いてあるので、Windowsを使ってる人なら誰でもMarkdownからdocxに変換出来ます。

pandocだと、わざわざpandocをインストールしなくてはいけないので、これからプロジェクトの仕様書はMarkdownで書く！なんてことは出来ませんが、このPowerShellならそれも可能・・・なはず。

ぜひブラッシュアップして本当に使えるようにしたいので、プルリクお願いします＞＜

