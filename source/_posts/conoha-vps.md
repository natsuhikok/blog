---
title: conoha VPSを契約してnodeアプリのテスト環境を作った
tags:
- vps
- linux
- node.js
description: conoha VPSを契約してnodeアプリのテスト環境を作った
date: 2016-10-10
---

ローカル環境で作っていたファイルを公開（テスト）するために、VPSを契約してみた。

## なぜconoha VPSを選んだか
herokuのようなツールと迷ったけどLinuxを触れる習慣がほしかたったのでconoha VPSを契約してみた。
サクラVPSも選択肢にあったけど、大きな違いはなさそうだったので何となくconohaを選んだ。深い意味はない。

## conohaの契約〜サーバーを建てる
申し込みをするを選んで進んで行くだけですぐサーバーを立ち上げるとこまでいける。ほぼ待ち時間なし。

若干迷ったのはSMS認証。契約したタイミングでタイに滞在していたので日本のSMSは受信できない。  
タイの携帯と住所で登録しようと思ったのだが国リストにタイの選択肢がなかなか見つからない。  
五十音順ではなく、国コード順になってると思われる。めちゃ不便だった。

## サーバータイプ
node.jsのアプリを動かしたかったので、vpsのイメージタイプをアプリケーション>MEAN stackを選択。
ssh接続するとMEAN.JSのwelcomeメッセージがでる。
https://meanjs.org/

上記参照すれば何ができるかわかると思う。とりあえず、mean.jsはとくに使わない予定。単純にnode/gitなどが最初から入ってる環境がほしかった。

## 色々設定する
とりあえず最低限の設定する。最低限の定義は曖昧です。

### ファイルをローカルのAtomで開けるようにする
コンソールエディタは使えないし使う気もないので、テキストエディタで開けるようにする。
[AtomでSSH越しのファイルを編集する](http://qiita.com/informationsea/items/d2576b1ab678965d9a02)

### サーバーのセキュリティ設定
まったくわからないので、[そこそこセキュアなlinuxサーバーを作る](http://qiita.com/cocuh/items/e7c305ccffb6841d109c)を参考に、というかそのまま星4以上をやりたい。

* [x] sshポート変更
* [x] sshでrootにlogin出来なくする
* [ ] パスワード認証を切る
* [ ] 使わないポートを閉じる
* [ ] ip spoofing対策
* [ ] バージョンを見えなくする
* [ ] 不要なサービスを止める

#### sshでrootにlogin出来なくする
まずはユーザーを作成する。[Linuxにユーザーを追加する](http://qiita.com/Tooooomin/items/c063cee80227a5f1d95b)
[Disable Root SSH Logon On CentOS 7](https://www.liberiangeek.net/2014/08/disable-root-ssh-logon-on-centos-7/)

`sudo systemctl restart sshd.service`する。

#### sshポート変更
[How To Change OpenSSH Port On CentOS 7](https://www.liberiangeek.net/2014/11/change-openssh-port-centos-7/)


sshd_configのポート番号だけ変更して、`sudo systemctl restart sshd.service`したらめんどくさいことになった。

#### 参考リンク
* [そこそこセキュアなlinuxサーバーを作る](http://qiita.com/cocuh/items/e7c305ccffb6841d109c)
* [SSHのポート番号を変更 - SSHサーバーの設定](http://webkaru.net/linux/change-ssh-port/)
