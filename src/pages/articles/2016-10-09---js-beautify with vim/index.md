---
layout: post
draft: false
cover: photo-1490474418585-ba9bad8fd0ea-cover.jpg
Categories:
- vim
Description: ""
Tags:
- vim
- js-beautify
- javascript
date: "2016-10-09T11:23:19+09:00"
title: js beautify with vim
---

vimでjavascriptを書くときの設定。

- `dein.toml`

```toml
# ==================== Filetype (javascript) ===== {{{1
[[plugins]]
repo = 'pangloss/vim-javascript'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'maxmellon/vim-jsx-pretty'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'othree/javascript-libraries-syntax.vim'
on_ft = ['javascript', 'javascript.jsx']

[[plugins]]
repo = 'othree/es.next.syntax.vim'
on_ft = ['javascript', 'javascript.jsx']

# ==================== Utility =================== {{{1
[[plugins]]
repo = 'Chiel92/vim-autoformat'
hook_add = '''
  let g:autoformat_autoindent = 0
  let g:autoformat_retab = 0
  let g:autoformat_remove_trailing_spaces = 0
  au MyAutoCmd BufWrite *.js,*.jsx :Autoformat
  nnoremap [Space]f :<C-u>Autoformat<CR>
'''
```

上記設定で、かつ、 `js-beautify` を別途インストールする。

```sh
$ npm i -g js-beautify
```

便利なのが、 `vim-autoformat` プラグイン。

`js-beautify` をインストールして上記設定をしていると、javascriptを書いて保存したら自動でインデントを整えてくれる。  
ただ、ES2015のimportやjsx表記などでインデントが変になってしまうので、`js-beautify` 用の設定ファイルを作成する。

- `.jsbeautifyrc`

```
{
  "indent_size": 2,
  "indent_char": " ",
  "eol": "\n",
  "indent_level": 0,
  "indent_with_tabs": false,
  "preserve_newlines": true,
  "max_preserve_newlines": 10,
  "jslint_happy": false,
  "space_after_anon_function": false,
  "brace_style": "collapse-preserve-inline",
  "keep_array_indentation": false,
  "keep_function_indentation": false,
  "space_before_conditional": true,
  "break_chained_methods": false,
  "eval_code": false,
  "unescape_strings": false,
  "wrap_line_length": 0,
  "wrap_attributes": "auto",
  "wrap_attributes_indent_size": 4,
  "e4x": true,
  "end_with_newline": false
}
```

これで快適なエディットができる。

- - -
##### 参考

[Newline inserted after ES6 module import/export · Issue #382 · beautify-web/js-beautify · GitHub](https://github.com/beautify-web/js-beautify/issues/382)

[GitHub - MaxMEllon/vim-jsx-pretty: [VimL] React JSX syntax pretty highlighting for vim.](https://github.com/MaxMEllon/vim-jsx-pretty)

