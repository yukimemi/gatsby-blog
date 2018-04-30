---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- hugo
Description: ""
Tags:
- hugo
- highlight
date: "2016-12-10T22:01:28+09:00"
title: Use highlight.js with CDN
---

[highlight.js](https://highlightjs.org/) を、 CDN で使用する方法。

このブログを書くのに今までは [google/code-prettify](https://github.com/google/code-prettify) を使っていたんだけど、うまく hightlight されないのがあったりしたので、 [highlight.js](https://highlightjs.org/) に乗り換えてみた。

使い方は非常に簡単。

ヘッダーに以下3行を追加するだけ。(スタイルは `onedark` を使用)

```html
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/styles/atom-one-dark.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

ただ、これでは基本的な言語のハイライトしか設定出来ない。
`powershell` とか、 `golang` とかも対応したい場合、個別に追加する必要がある。

[highlight.js](https://highlightjs.org/) のサイトで個別に言語を指定してダウンロードすることもできるが、全部ポチポチ指定してダウンロードするのは面倒だったので、直接 CDN から使いたかった。

調べると、 [isagalaev/highlight.js](https://github.com/isagalaev/highlight.js) の README に書いてあった。
個別に使いたい言語を CDN から使うように指定すればいいみたい。

ということで、以下のようになりました。

```html
<!-- hilight.js -->
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/styles/atom-one-dark.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/highlight.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/languages/vim.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/languages/dos.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/languages/go.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/languages/powershell.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.8.0/languages/haskell.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

べんり。

- - -

##### 参考

[コードのハイライト表示用 JS ライブラリ highlight.js の使い方](http://qiita.com/tadnakam/items/99088d78512a20e75ff3)

[軽量で多言語なコードハイライトhighlight.jsの使い方](http://glatchdesign.com/blog/web/javascript/296)

