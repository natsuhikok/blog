---
title: コマンドラインからgithubを操作したい
tags:
- github
- git
- cli
description: hubを使ってコマンドラインからgithubを操作したい。sshキーの登録とか。
date: 2016-08-02
---

コマンドラインから`github`にレポジトリを作りたかった。
いちいち`github`にアクセスして`create repository`するのはめんどくさすぎた。

## github/hub
githubが公開している[github/hub](https://github.com/github/hub)を使う。

## インストール
macはhomebrewから一発でインストールできるけれど、windowsへのインストールがめんどうだった。

### macのインストール
`$ brew install hub`のみ。

### windowsのインストール
homebrewは使えないので`go lang`を使ってインストールする（はずだった）。`go`をインストールするところから。
[The Go Programming Language - Getting Started](https://golang.org/doc/install)からwindows用インストールして。`hub`をインストールする。

~~~bash
$ git clone https://github.com/github/hub.git
$ cd hub
$ ./script/build -o ~/bin/hub
~~~

`go path`(`~/bin/hub`の部分)はどこでもいいはず。

なぜかパスワードを要求されるところで`Exception`がでてうまくいかなかった。
ルートにある`bun/hub`を削除して、[windows用の実行ファイル(`bin/hub.exe`)](https://github.com/github/hub/releases)に置き換えたら動いた。
好きならところに`bin/hub.exe`を設置してエイリアスを入れるだけでよかったのかも。

## SSH接続の設定
ssh接続が必要になるので、ssh keyを作成してgithubに教える必要がある。

keyの作成手順は、windows/mac共通で`$ ssh-keygen`するだけ。エンターを３回押すとルートの`.ssh`に`id_rsa`と`id_rsa.pub`が生成される。すでに別のsshキーを使っている場合やパスワードを設定したい場合は、`$ ssh-keygen`で指示に従って適当に設定する。

github側の設定は`setting > SSH and GPG keys > new ssh key`から`id_ras.pub`の中身をコピペする。タイトルは任意のもの。


## コマンド
ぼくが使いたかったのはレポジトリを作成だけなんだけれど。
他にも便利そうなコマンドがあった。

今いるディレクトリのgithubレポジトリをブラウザで開く。

~~~bash
$ hub browse
$ hub browse [user_name] # ユーザーページを表示
~~~

今いるディレクトリのgithubレポジトリを作成してpushする。originの設定もしてくれる。

~~~bash
$ hub create
$ hub create -p # private repository
~~~

## 課題
* ~~プライベートレポジトリも作りたい~~
