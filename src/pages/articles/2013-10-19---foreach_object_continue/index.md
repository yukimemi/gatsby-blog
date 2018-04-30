---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- powershell
Description: ""
Tags:
- powershell
- ForEach-Object
date: "2013-10-19"
title: powershell ForEach-Objectでcontinue
---

powershell の ForEach-Object で、continue しようと
したら、思ったように動かなかった話。

こんなコードを書いた。

```powershell
gci -r |
% {
   if ($_.Name -match "^tes.*") {
      continue
   }
   $_
}
```

現在のディレクトリ配下を再帰的に見て、ファイル名が tes
から始まるもの以外を 表示する・・・みたいな処理。 マッチした場合のみ、
continue して次の処理に行きたいのだが、この書き方だと、 break
したのと同じような動きになってしまい、初回マッチしたら、それ以降の処理
はされない。

やりたいことを満たすには、以下のように書けばいいみたい。

```powershell
gci -r |
% {
   if ($_.Name -match "^tes.*") {
      return
   }
   $_
}
```

continue の代わりに return を使う。

##### 参考:

[powershell - Why does continue behave like break in a Foreach-Object? - Stack Overflow](http://stackoverflow.com/questions/7760013/why-does-continue-behave-like-break-in-a-foreach-object)

