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
date: "2017-03-19T00:00:07+09:00"
title: Git status recurse
---

`git status` を再帰的に全Gitディレクトリで実行して、変更のあるリポジトリを簡単に見つけたい。

と思ったので作りました。

[yukimemi/gsr](https://github.com/yukimemi/gsr)

`go get` でインストールできます。

(バイナリファイルもそのうち用意する)


```sh
$ go get github.com/yukimemi/gsr
```

使い方は簡単で

```sh
$ gsr [再帰的にチェックしたいディレクトリ]
```

とすると、 `git status` で変更のあるディレクトリだけ出力されます。
デフォルトでは、ディレクトリ名だけが出力されます。

```sh
$ gsr --status [再帰的にチェックしたいディレクトリ]
```

と、オプションをつけると、 `git status --short` の出力結果も出力されます。

ちなみに、 [再帰的にチェックしたいディレクトリ] を指定しなかった場合、
`ghq root` を対象にチェックします。

[motemen/ghq](https://github.com/motemen/ghq) を使っている人には便利です。


デフォルトの出力がディレクトリ名なのは、pecoとか使って移動したいからです。

例えば、 `.zshrc` に、こんな設定を書いてやると、

```zsh
function __filter() {
  peco | while read line
  do
    echo "Exec: [$@ $line]"
    $@ $line
  done
}
alias gsrl='gsr | __filter cd'
alias ghl='gsr --all | __filter cd'
```

`gsrl` で、 `ghq` で管理された変更のあるgitディレクトリに移動出来るようになり、 `ghl` で `ghq` で管理されたすべてのディレクトリに移動出来るようになります。

非常にべんり。

さらに、 リモートと差分のあるリポジトリも対象としたい場合、以下のオプションが使用できます。

```sh
$ gsr --ahead --behind
```

| オプション | 出力対象 |
|:----------:|:--------:|
| --ahead    | リモートより新しいものも対象にする |
| --behind   | リモートより古いものも対象にする |


僕は常にこのオプションを有効化して使うので、環境変数に指定しています。
`.zshenv` とかに書いておくと、実行時にオプションをいちいち指定しなくても有効化されます。

```zsh
export GSR_SHOW_AHEAD=1
export GSR_SHOW_BEHIND=1
```

これは、使ってるライブラリ [urfave/cli](https://github.com/urfave/cli) の機能みたいです。すごい。

- - -

##### 参考

[urfave/cli](https://github.com/urfave/cli)

[git statusを利用したリポジトリ情報のプロンプト表示](http://int128.hatenablog.com/entry/2015/07/15/003851)


