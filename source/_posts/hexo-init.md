---
title: blogをpowered by Hexoにした。
date: 2016-05-28
tags:
- node.js
- javascript
description: blog generator を hexo に乗り換えた話と覚え書きとか。
---

`middleman blog`でgenerateして`github page`でホストしていたblogを`hexo`に代えた。ホストは、`github page`のまま。

## why hexo
blogの投稿自体を長いこと放置していたこともあって`middleman blog`の扱い方がさっぱりになっていた。普段触っている言語も`ruby`から`javascript`になっていたこともあって、`javascript`の`hexo`に乗り換えた。

記事は、`markdown`で書いていたので簡単に移行できたのだけれど、独自の`front matter`を作っていたのでそこをどうするかで悩んだ。(結局捨てた)

## hexo command
`hexo`でよく使いそうなコマンド。

サーバーの起動。作成した記事を確認するときとか。
```bash
$ hexo s
```

`draft`の記事を含めて確認することのが多いかも。
```bash
$ hexo s --draft
```

publicの生成。
```bash
$ hexo g
$ hexo clean // publicフォルダを空にする
```

デプロイ。`_config.yml`で設定した`deploy`先にアップロードする。ぼくは、前と同じ`github page`の`user page`にしている。
```bash
$ hexo deploy
# generate してそのまま deployする
$ hexo deploy --generate
```

## hexo markdown
`hexo`独自のmarkdown基本が存在する。`code block`に`caption`をつけるのとか便利そう。(これしか知らない)
```markdown
{% codeblock this/is/caption.js lang:javascript %}
code...
{% endcodeblock %}
```
以下のようにキャプション付きのコードブロックになる。引越しを考えると積極的に使いたくない気もする。

{% codeblock gulp/sass.js lang:javascript %}
//こんな感じにハイライトされる。
const gulp        = require('gulp');
const sass        = require('gulp-sass');
const plumber     = require('gulp-plumber');
/////////////////////////////////////////
// SCSS
/////////////////////////////////////////
gulp.task('sass', () => {
  gulp.src('source/scss/**')
    .pipe(plumber())
    .pipe(sass({outputStyle: 'compressed'}))
    .pipe(gulp.dest('source/css/'))
    .pipe(browsersync.reload({stream: true}));
});
gulp.task('watch', () => {
  gulp.watch(['source/scss/**'],['sass']);
});
{% endcodeblock %}


## 抱負
ちょくちょく更新していきたい。
