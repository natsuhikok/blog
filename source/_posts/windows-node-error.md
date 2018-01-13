---
title: windows環境でのnode-sassのエラー
tags:
- node.js
- windows
description: windows環境でのnode-sassのエラーと解決
date: 2016-12-19
---
windows環境でのnode-sassのエラーがでた。

~~~
$ gulp
~~~

するとエラーがでた。

### 実行環境
~~~
windows: 10
node: 4.4.7
npm: 3.10.5
nodist: 0.84
gulp CLI: 3.9.1
gulp Local: 3.9.1
~~~

### エラーログ
~~~
Node Sass could not find a binding for your current environment: Windows 64-bit with Node.js 6.x

Found bindings for the following environments:
  - Windows 64-bit with Node.js 4.x

This usually happens because your environment has changed since running `npm install`.
Run `npm rebuild node-sass` to build the binding for your current environment.
~~~

### 解決
指示通り`npm rebuild node-sass`を試したものの同じエラーがでたが、nodeを6.xにしたらエラーがでなくなった。

### 不明点
インストールやnode -v の結果は、4.xだったのにnode-sassは6.xを使おうとしてたみたい。なんでそうなったのか不明。インストールが4.xになってるために`npm rebuild node-sass` では解決されなかったということなんだろう。

いろいろ不明点が残る。windows環境での情報少ない・・。

### 参考
* [node 4.0.0 - Error: The libsass binding was not found in /node_modules/gulp-sass/node_modules/node-sass/vendor/darwin-x64-14/binding.node](https://github.com/sass/node-sass/issues/1162)
* [libsass bindings not found when using node-sass in nodejs](http://stackoverflow.com/questions/29461831/libsass-bindings-not-found-when-using-node-sass-in-nodejs)
