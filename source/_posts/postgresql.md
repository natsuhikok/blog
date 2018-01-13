---
title: node.js & postgreSQL
tags:
- node.js
- database
- postgreSQL
description: node.jsでpostgreSQLを触ってみた。
date: 2016-07-16
---
`postgreSQL`を触ってみた。`node.js`といえば`mongoDB`という安易な考えで、データベースを選んでいたけれど、ふと`SQL`のほうがいいんじゃないと思った。
ちなみに、オブジェクトを雑に突っ込める`mongoDB`は軽くいじる分には楽でよかった。


## windowsにpostgreSQLをインストールする
[公式サイト](https://www.postgresql.org/)からインストーラーをダウンロードしてインストールするだけ。
インストール後に`Stack Builder`とかいう追加アプリケーションをインストールするアプリケーションが起動するのだけれど、無視していいっぽい。

## macにpostgreSQLをインストールする
[`Postgres.app`(http://postgresapp.com)](http://postgresapp.com)を使うのが良さそう。
GUIアプリは、[`Postico`(https://eggerapps.at/postico/)](https://eggerapps.at/postico/)がよさげ。

## コマンド
使いそうなコマンドとか。
デフォルトのロールは`postgres`。ロールという名称が謎だけど、ユーザー名的なもののよう。

接続する。
~~~bash
$ psql
$ psql -U [role_name]
$ psql -U [role_name] -d [database_name]
## 他のデータベースに接続しなおす
\connect [database_name]
~~~

表示する。
~~~bash
## ロール一覧
\du
## テーブル一覧
\d
## テーブルのフィールド一覧
\d [table_name]
## データベースの一覧
\l
~~~

作成・削除。
~~~bash
## データベースの作成
create database [database_name];
## テーブル追加・削除
create table [table_name];
drop table [table_name];
~~~

## node.jsからpostgreSQLを使う
`pg`を使う。データベース`test`にテーブル`items`を作る。やらかさないように`idpass`は別ファイルから読み込む。

{% codeblock pg_with_callback.js lang:javascript %}
var pg = require('pg');
var idpass = require('./idpass.js')
var db_config = {
  user: idpass.user,
  password: idpass.password,
  database: 'test'
}
var query = 'CREATE TABLE items(id SERIAL PRIMARY KEY, text VARCHAR(40) not null, complete BOOLEAN)';
pg.connect(db_config, function(err, client, done) {
  if(err) {
    return console.error('error connection', err);
  }
  client.query(query, function(err, result) {
      if(err) {
        return console.error('error insert', err);
      }
  });
});
{% endcodeblock %}

シンプルに`callback`を使わないとこうなる。
{% codeblock pg_without_callback.js lang:javascript %}
var pg = require('pg');
var idpass = require('./idpass.js')
var db_config = {
  user: idpass.user,
  password: idpass.password,
  database: 'test'
}
var client = new pg.Client(db_config);
client.connect();
var query = client.query('CREATE TABLE items(id SERIAL PRIMARY KEY, text VARCHAR(40) not null, complete BOOLEAN)')
query.on('end', client.end.bind(client));
{% endcodeblock %}

## 参考にしたサイト
* [node-postgres/wiki](https://github.com/brianc/node-postgres/wiki)
* [npm pg](https://www.npmjs.com/package/pg)
* [PostgreSQL and NodeJS](http://mherman.org/blog/2015/02/12/postgresql-and-nodejs/#.V18bM_mLRhE)
