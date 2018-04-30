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
- sns
- button
date: "2013-06-15"
title: Tinkerer にソーシャルボタン追加
---

今時のブログとしては、ソーシャルボタンは追加したい。

参考になる記事がいっぱいあったので、参考にしてやってみた。

### 事前準備

layout.html というファイルと page.html というファイルを作る。

layout.html は全体？ page.html
は記事一つ一つみたいな感じがする。たぶん。

```sh
$ vim _templates/layout.html
```

中身はこんな感じ。

```html
{% extends "!layout.html" %}

{% block extrahead %}
<!-- ここに html の ヘッダ部分に追加するスクリプトを書く -->
{% endblock %}
```

ね。簡単でしょ。 1行目で既存の layout.html を継承してる。たぶん

layout.html では、extrahead というブロックを使うことで、既存の
\<head\>タグに追加の情報を出力することが出来る。

次に page.html

```sh
$ vim _templates/page.html
```

中身はこんな感じ。

```html
{% extends "!page.html" %}

{% block body %}
{{ timestamp(metadata.formatted_date) }}
{% block buttons %}
<!-- ここに ソーシャルボタンのコードを追加していく -->
{% endblock %}
{{ body }}
{{ self.buttons() }}
{{ post_meta(metadata) }}
{{ comments }}
{% endblock %}
```

ソーシャルボタンは記事の上と下に表示したいため、下では上の定義を
`{{ self.buttons() }}` という形 で参照している。

これで事前準備は完了。

### Twitter ボタン

ボタン作成のコードをググって取得する。

Twitter のボタンは\<head\>部分のスクリプトは必要ないみたいなので、
page.html にコードを記述する。

```html
<!-- Twitter button -->
<a href="https://twitter.com/share" class="twitter-share-button" data-lang="ja">ツイート</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>
```

それから、デフォルトでは横幅が広すぎるので css を調整する。
[前回](http://yukimemi.bitbucket.org/blog/html/2013/06/13/change_theme.html)
の記事で自分の theme を 作っていたら、それに追記する。

```sh
$ vim _themes/mytheme/static/modern5.css_t
```

追加するcssはこんな感じ。

```css
/* Twitter button */
iframe.twitter-share-button {
width: 88px!important;
}
```

### hatena ボタン

ボタン作成のコードをググって取得する。

hatena のボタンは
\<head\>部分にも追加のスクリプトがあるみたいなので、まず layout.html
の `{% block extrahead %}` に追記する。

```html
<!-- for hatena -->
<script type="text/javascript" src="http://b.st-hatena.com/js/bookmark_button.js" charset="utf-8" async="async"></script>
```

次にボタン部分を page.html に追加する。

```html
<!-- Hatena button -->
<a href="http://b.hatena.ne.jp/entry/http://yukimemi.bitbucket.org/{{ pagename }}{{ file_suffix }}" class="hatena-bookmark-button" data-hatena-bookmark-title="{{ title }} - {{ project }}" data-hatena-bookmark-layout="standard-balloon" title="このエントリーをはてなブックマークに追加"><img src="http://b.st-hatena.com/images/entry-button/button-only.gif" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
```

URLやタイトルをそのページにあったものに変換している。 `{{ pagename }}`
、 `{{ file_suffix }}` などは ググればわかる。

### google+ ボタン

ボタン作成のコードをググって取得する。

まず\<head\>部分に追加するスクリプトを layout.html に追記。

```html
<!-- for gogogle + -->
<script type="text/javascript">
window.___gcfg = {lang: 'ja'};

(function() {
 var po = document.createElement('script'); po.type = 'text/javascript'; po.async = true;
 po.src = 'https://apis.google.com/js/plusone.js';
 var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
 })();
</script>
```

それから、ボタン部分を page.html に追記。

```html
<!-- Google+ button -->
<div class="g-plusone" data-size="medium"></div>
```

Google+ は簡単でいいね。

### facebook ボタン

ボタン作成のコードをググって取得する。 これは app id
とか取得しなきゃいけなくてけっこうめんどくさい。

取得したら、\<head\>部分に追加するスクリプトを layout.html に追記。
channelUrl
とかいうのが全然意味わからんかった。誰か知ってる人教えて下さい。

```html
<!-- for facebook -->
<div id="fb-root"></div>
<script>
window.fbAsyncInit = function() {
  // init the FB JS SDK
  FB.init({
appId      : 'XXXXXXXXXXXXXXX',                      // App ID from the app dashboard
channelUrl : '//yukimemi.bitbucket.org/channel.html', // Channel file for x-domain comms
status     : true,                                   // Check Facebook Login status
xfbml      : true                                    // Look for social plugins on the page
});

// Additional initialization code such as adding Event Listeners goes here
};

(function(d, s, id) {
 var js, fjs = d.getElementsByTagName(s)[0];
 if (d.getElementById(id)) return;
 js = d.createElement(s); js.id = id; js.async = true;
 js.src = "//connect.facebook.net/ja_JP/all.js#xfbml=1";
 fjs.parentNode.insertBefore(js, fjs);
 }(document, 'script', 'facebook-jssdk'));
</script>
```

ちなみに、非同期にすることで早くなるらしい。

次にボタンを配置する。 page.html に追加する。

```html
<!-- facebook button -->
<div class="fb-like" data-href="http://yukimemi.bitbucket.org/{{ pagename }}{{ file_suffix }}" data-send="false" data-layout="button_count" data-width="200" data-show-faces="true"></div>
```

これでおｋ。

全体は以下の通り。

layout.html

```html
{% extends "!layout.html" %}

{% block extrahead %}
<!-- for hatena -->
<script type="text/javascript" src="http://b.st-hatena.com/js/bookmark_button.js" charset="utf-8" async="async"></script>

<!-- for gogogle + -->
<script type="text/javascript">
window.___gcfg = {lang: 'ja'};

(function() {
 var po = document.createElement('script'); po.type = 'text/javascript'; po.async = true;
 po.src = 'https://apis.google.com/js/plusone.js';
 var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(po, s);
 })();
</script>

<!-- for facebook -->
<div id="fb-root"></div>
<script>
window.fbAsyncInit = function() {
  // init the FB JS SDK
  FB.init({
appId      : 'XXXXXXXXXXXXXXX',                      // App ID from the app dashboard
channelUrl : '//yukimemi.bitbucket.org/channel.html', // Channel file for x-domain comms
status     : true,                                   // Check Facebook Login status
xfbml      : true                                    // Look for social plugins on the page
});

// Additional initialization code such as adding Event Listeners goes here
};

(function(d, s, id) {
 var js, fjs = d.getElementsByTagName(s)[0];
 if (d.getElementById(id)) return;
 js = d.createElement(s); js.id = id; js.async = true;
 js.src = "//connect.facebook.net/ja_JP/all.js#xfbml=1";
 fjs.parentNode.insertBefore(js, fjs);
 }(document, 'script', 'facebook-jssdk'));
</script>
{% endblock %}
```

page.html

```html
{% extends "!page.html" %}

{% block body %}
{{ timestamp(metadata.formatted_date) }}
{% block buttons %}
<!-- facebook button -->
<div class="fb-like" data-href="http://yukimemi.bitbucket.org/{{ pagename }}{{ file_suffix }}" data-send="false" data-layout="button_count" data-width="200" data-show-faces="true"></div>

<!-- Hatena button -->
<a href="http://b.hatena.ne.jp/entry/http://yukimemi.bitbucket.org/{{ pagename }}{{ file_suffix }}" class="hatena-bookmark-button" data-hatena-bookmark-title="{{ title }} - {{ project }}" data-hatena-bookmark-layout="standard-balloon" title="このエントリーをはてなブックマークに追加"><img src="http://b.st-hatena.com/images/entry-button/button-only.gif" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>

<!-- Twitter button -->
<a href="https://twitter.com/share" class="twitter-share-button" data-lang="ja">ツイート</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+'://platform.twitter.com/widgets.js';fjs.parentNode.insertBefore(js,fjs);}}(document, 'script', 'twitter-wjs');</script>

<!-- Google+ button -->
<div class="g-plusone" data-size="medium"></div>
{% endblock %}
{{ body }}
{{ self.buttons() }}
{{ post_meta(metadata) }}
{{ comments }}
{% endblock %}

{% set script_files = script_files + ["_static/google_analytics.js"] %}
```

##### 参考:

[Tinkererで作ったブログにソーシャルボタンを追加する—鉄馬の工具箱](http://te2uma.com/blog/2013/03/29/tinkerer_with_social_button.html)

[テンプレート — Sphinx v1.0.6 documentation](http://sphinx-users.jp/doc10/templating.html#id2)

[テンプレート — Sphinx v1.0.6 documentation](http://sphinx-users.jp/doc10/templating.html#id6)

[XFBML版Facebookの「いいね！」ボタンを設置する。 - The Web Laboratory](http://happyweblife.main.jp/mtarcive/laboratory/2011/03/xfbmlfacebook.html)

[たった一言で「いいね！」ボタンが爆速に！全ブロガーに必須の呪文。| iPhone 研究室](http://iphone-lab.net/archives/253683)

