---
title: 新しいMacbookを買ったの開発環境を作った
tags:
- mac
- node.js
- zsh
- cli
description: 新しいMacbookを買ったの開発環境を作った
date: 2017-01-27
---
Macbookを買ったのでその設定とかやってた。

## キーボードの設定
USキーボードなので日本語入力をできるようにする。`google日本語入力`をインストールしてみたけど、なぜか動かなかったのでデフォルトの日本語入力を使う。

以前は外付けキーボードの設定に`karabiner`を使ったいたのだけど、`Sierra`に対応していない。が`sierra`向けに開発中の`karabiner elements`でで設定できた。以下、設定。

* 外付けキーボード接続時にbuilt-inキーボードを無効
* right_altキーに存在しないF13を割り当てる

OS環境設定>キーボードからaltを入力切替に割り当てた。

## Macで隠しファイルを表示する
`.gitignore`などの隠しファイルを`Finder`で表示させる。

```bash
$ defaults write com.apple.finder AppleShowAllFiles TRUE
$ defaults read com.apple.finder AppleShowAllFiles
>> TRUE
$ killall Finder
```

`sierra`からは`.DS_Store`が表示されない？

## CL環境を作る
参考: iTerm2 と zsh のインストールと基本的な設定( [iTerm2 と zsh のインストールと基本的な設定 - Qiita](http://qiita.com/bird_tummy/items/bd063f0d7eb18287fec1) )

### ターミナルの代わりにIterm2をインストール
`Preferences>General>Preferences`から設定ファイルを移行できる。

### zshのインストール
`Zsh`のインストールに`Homebrew`が必要。`homebrew`のインストールに`x-code`の`command line tools`が必要。

`x-code`を`app store`からインストールして`$ xcode-select --install`で`command line tools`をインストールする。

`homebrew`( [Homebrew — The missing package manager for macOS](http://brew.sh/) )をインストールする。
` $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" `
事前に`xcode`のライセンスに同意する必要がある、指示に従ってclから可能。インストールが済んだら`$brew doctor`でエラーをチェックしとく。

Zshとoh-my-zshをインストールする。

```bash
$ brew install zsh
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```

とりあえずzshのインストールはこれで完成。windowsと違って環境変数とかいじらないので楽。前の環境から`.zshrc`をお好みでコピーする。

## gitのインストール
Macには最初からgitが入っているので`$ brew install git`でアップデートする。`git config`の移行はルートフォルダの`.gitconfig`を移すだけ。
### githubの設定
前に書いたメモを参照: [コマンドラインからgithubを操作したい | hikonaz blog](http://hikonaz.com/2016/08/githubhubhub/)


## nodeのインストール
Node Version Manager - https://github.com/creationix/nvm をインストールする。
```bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
```

`.zshrc`から`nvm`にパスを通す。

```bash
$ export NVM_DIR=“$HOME/.nvm”
$ [ -s “$NVM_DIR/nvm.sh” ] && . “$NVM_DIR/nvm.sh” # This loads nvm
```

Nodeをインストールする。

```bash
$ nvm install v6.9.4
$ nvm use v6.9.4
# ↓インストールできているか確認
$ node -v
$ npm -v
```

## MAMPの移行
Macの設定とは直接関係ないけれど、 作業途中のwordpressプロジェクトも移行する。一番めんどくさそうだと思ったけどわりと簡単だった。イカ手順。

1. 新MAMP上にWodpressをインストールする
2. 旧MAMP>phpadminから該当データベースを抜き取る
3. 新MAMPの該当のデータベースを空にして2で抜き取ったデータをインポートする
4. wp-contentを入れ替えたら完成

多分MAMPのポートを合わせないと失敗する。

## その他の細かい設定とか
* atomの設定: .atomを丸ごとresillio syncで同期した。

## インストールしたアプリケーション
* google chrome
* karabiner elements
* atom
* simple note
* Todoist
* caffeine
* mamp - https://www.mamp.info/en/
* Slicy
* Line
* resillio sync
* skype
* photoshop
* illustrator
* istat menu
* source tree
* tomato one
* express vpn
* iterm2 - http://iterm2.com/

## TODO
残りしなきゃいけないこと。

* safariの同期
* photoshopの設定同期
