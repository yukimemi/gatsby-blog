---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- JScript
- ES2015
Description: ""
Tags:
- JScript
- webpack
- babel
- gulp
- ES2015
date: "2016-07-26T00:58:51+09:00"
title: JScript by ES2015 with webpack x gulp x babel
---

JScriptをES2015で書こう！！

・・・今更なJScriptだけれども、まだまだWindowsでは使えるはず。
いや、仕方なく仕事では使わなきゃいけない場面が多々ある。

最近はpowershellをよく使っていたけれども、書いてて楽しいのはやっぱりjavascriptの方が上。
でもどうせ書くなら、ES2015で書きたい！！

そこで、babelやらwebpackやらgulpやらを駆使してやってみた。

ディレクトリ構成はこんなの。
```sh
$ tree -I node_modules
.
├── src
│   ├── foo.js
│   └── bar.js
├── dst
│   ├── foo.cmd
│   └── bar.cmd
├── .babelrc
├── package.json
├── gulpfile.babel.js
└── webpack.config.babel.js
```

いろいろインストール。(こんだけやったか定かじゃないけど・・・
```sh
$ npm i -D babel babel-loader babel-preset-es2015-loose es3ify-webpack-plugin gulp gulp-cli gulp-convert-encoding gulp-eol gulp-header gulp-plumber gulp-rename gulp-watch webpack webpack-stream
```

`package.json` はこんな感じ。

- `package.json`

```json
{
  "name": "jscript",
  "version": "1.0.0",
  "description": "",
  "main": "gulpfile.babel.js",
  "dependencies": {},
  "devDependencies": {
    "babel-core": "^6.9.1",
    "babel-loader": "^6.2.4",
    "babel-plugin-transform-es2015-ie8-classes": "^0.1.8",
    "babel-polyfill": "^6.9.1",
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-es2015-loose": "^7.0.0",
    "babel-preset-es2015-loose-ie8": "0.0.4",
    "es3ify-webpack-plugin": "0.0.0",
    "es5-shim": "^4.5.8",
    "gulp": "^3.9.1",
    "gulp-cli": "^1.2.2",
    "gulp-convert-encoding": "^1.0.0",
    "gulp-eol": "^0.1.2",
    "gulp-header": "^1.8.7",
    "gulp-plumber": "^1.1.0",
    "gulp-rename": "^1.2.2",
    "gulp-replace": "^0.5.4",
    "gulp-watch": "^4.3.9",
    "imports-loader": "^0.6.5",
    "json3": "^3.3.2",
    "lodash": "^3.10.1",
    "moment": "^2.14.1",
    "webpack": "^1.13.1",
    "webpack-stream": "^3.2.0"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "gulp",
    "watch": "gulp watch"
  }
}
```

いらないのあるかもしれない。
ポイントとしては、 `lodash` を使いたい場合、Version 4.0 以降はie8以下に対応してないので、(つまりJScriptも不可) あえてVersionをダウンさせる。


次に `webpack` の設定。

- `webpack.config.babel.js`

```javascript
import path from 'path';
import encodingPlugin from 'webpack-encoding-plugin';
import es3ifyPlugin from 'es3ify-webpack-plugin';
import webpack from 'webpack';

const src = path.join(__dirname, 'src');

export default {
  entry: {
    foo: path.join(src, 'foo.js'),
    bar: path.join(src, 'bar.js')
  },
  output: {
    path: path.join(__dirname, 'dst'),
    filename: '[name].js'
  },

  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js']
  },
  plugins: [
    new es3ifyPlugin(),
    new webpack.ContextReplacementPlugin(/moment[\\\/]locale$/, /^\.\/(en|ja)$/),
  ]

};
```

スクリプトってのはその場でちょーっと書くものだから、一つじゃなくてentry pointは複数にしてる。
ポイント？としては、 `es3ify-webpack-plugin` で、es3へダウンコンパイル？させてることと、 `moment.js` を使う場合、そのままではwebpackが全ロケールを読みこんじゃうんで、一部だけ読み込むようにしてるところ。

`webpack` でばべる時の設定で、JScriptとして使えるようにするために(IE8以下対応) `.babelrc` の設定をする。

- `.babelrc`

```javascript
{
  "presets": [
    "es2015-loose"
  ]
}
```

んで、これが `gulp` の設定。

- `gulpfile.babel.js`

```javascript
import gulp from 'gulp';
import encoding from 'gulp-convert-encoding';
import eol from 'gulp-eol';
import plumber from 'gulp-plumber';
import watch from 'gulp-watch';
import rename from 'gulp-rename';
import header from 'gulp-header';
import webpack from 'webpack-stream';

import config from './webpack.config.babel.js';

const Header = "@set @junk=1 /*\r\n@cscript //nologo //e:jscript \"%~f0\" %*\r\n@exit /b %errorlevel%\r\n\r\n*/";

gulp.task('build', () => {
  gulp.src('src')
    .pipe(plumber())
    .pipe(webpack(config))
    .pipe(eol("\r\n"))
    .pipe(encoding({to: "Shift_JIS"}))
    .pipe(rename({extname: ".cmd"}))
    .pipe(header(Header))
    .pipe(gulp.dest('dst'));
});

gulp.task('watch', ['build'], () => {
  watch(["./src/**"], (evt) => {
    gulp.start('default');
  });
});

gulp.task('default', ['build']);
```

1. webpack実行
2. 改行コードを `CRLF` へ変更
3. 文字コードを `Shift_JIS` へ変換
4. 拡張子を `.cmd` へ変更
5. ヘッダーを付与 (バッチファイルとして実行させるため)

ってことをしてる。

実行するときは、 `npm script` で、

```sh
$ npm run build
```

watch タスクは
```sh
$ npm run watch
```

これで快適にJScriptがかける！！

- - -
##### 参考

[javascript - How to prevent moment.js from loading locales with webpack? - Stack Overflow](http://stackoverflow.com/questions/25384360/how-to-prevent-moment-js-from-loading-locales-with-webpack)

[BryceHQ/es3ify-webpack-plugin: A simple webpack plugin to es3ify your code for old versions of ie, such as ie8.](https://github.com/BryceHQ/es3ify-webpack-plugin)


