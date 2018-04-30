---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- python
Description: ""
Tags:
- python
- tinkerer
- theme
date: "2013-06-13"
title: Tinkererでテーマカスタマイズ
---

Tinkererのテーマをカスタマイズしたいと思った。

カスタマイズといってもそんな大層なことがしたいわけではなく。

デフォルトの "modern5" だと、横幅が狭いと思ったので広くしたかっただけ。
今時のワイド画面が多い中ではもうちょっと広くてもいいでしょう。

カスタマイズの方法は、まず元になるテーマである "modern5"
をコピーしてくる。

```sh
$ mkdir -p _themes/mytheme
$ cp -r ~/.virtualenvs/2.7.4/lib/python2.7/site-packages/tinkerer/themes/modern5/_themes/mytheme
```
それから、conf.pyにmythemeを使うことを明記する。

```python
# Pick another Tinkerer theme or use your own
html_theme = "mytheme"

# Add other theme paths here
html_theme_path = ['_themes', tinkerer.paths.themes]
```

後は `_themes/mytheme/static/modern5.css_t` を編集すればいいみたい。

よくわからないけど、 width がついているところを全部 +200px
したった。

##### 参考:

[Tinkererを使ってblogを始める - KRAKENBEAL RECORDS](http://krakenbeal.blogspot.jp/2012/05/tinkererblog.html)

