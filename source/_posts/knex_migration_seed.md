---
title: postgreSQL with knex.js (1) - migrate and seed
tags:
- postgreSQL
- Knex.js
- node.js
- database
description: knex.jsでpostgreSQLを操作する。マイグレーションとシードで、テーブルとテストデータを作る。
---

`knex.js`は`SQL QueryBuilder`。これで`postgreSQL`を操作する。マイグレーションとシードでテーブルとテストデータを作るところから。

## インストールする
`knex`と`pg`をインストールする。

~~~bash
$ npm install knex pg --save
~~~

CLIで操作をするこになるのでグローバルにもインストールする。
~~~bash
$ npm install knex -g
~~~

別途`postgreSQL`のインストールが必要。`postgreSQL`と`pg`については、[node.js & postgreSQL](http://hikonaz.com/2016/07/postgresql/)を参照。

## テーブルを作って壊して作る
`knex.js`のチュートリアル的なポストをみると、`express`をインストールして・・。みたいな話になるけれど。まずは、データを作って壊して再建するとこまで。
ここでの`File Structure`は以下。

~~~
/
├── migrations....生成されたmigrationファイルが格納される
├── knexfile.js...knexの設定ファイル
└── package.json
~~~

### Create DB
データベースを作成。ブログのデータベースを作ることにする。

~~~bash
createdb blogdev
~~~

### knexfile.js
knexfile.jsはクライアント、環境、使うデータベースを管理する設定ファイル。ここでは`development`のみを設定する。

{% codeblock knexfile.js lang:javascript %}
module.exports = {
  development: {
    client: 'postgresql',
    connection: {
      database: 'blogdev'
    }
  }
}
{% endcodeblock %}

### マイグレーションファイルを作る
これでデータベースの設定は完了。続いてマイグレーションファイルを作る。

~~~bash
$ knex migrate:make blog
~~~

`blog`の部分は、なんでもオッケー。マイグレーションファイルの操作内容がわかるネーミングで。
`migrations`に`timestamp_blog.js`というマイグレーションファイルが出力される。

{% codeblock migrations/20160805114402_blog.js lang:javascript %}
exports.up = function(knex, Promise) {
};
exports.down = function(knex, Promise) {
};
{% endcodeblock %}

`exports.up`と`exports.down`はそれぞれ、DBに実行する操作と取り消す操作を入れる。

`exports.up`にフィールド`id`,`title`,`body`,`postDate`を持つテーブル`posts`を追加する操作を入れる。

{% codeblock migrations/20160805114402_blog.js lang:javascript %}
exports.up = function(knex, Promise) {
    return Promise.all([
        knex.schema.createTable('posts', function(table){
            table.increments('id').primary();
            table.string('title');
            table.string('body');
            table.dateTime('postDate');
        }),
    ])
};
...
{% endcodeblock %}

`exports.down`にはテーブル`posts`をドロップする操作を入れる。

{% codeblock migrations/20160805114402_blog.js lang:javascript %}
...
exports.down = function(knex, Promise) {  
    return Promise.all([
        knex.schema.dropTable('posts')
    ])
};
{% endcodeblock %}

### マイグレーションを実行する
操作を追加したマイグレーションファイルを使う。

~~~bash
$ knex migrate:latest
~~~

もっとも新しいマイグレーションファイル(さっき`up`と`down`を入力したファイル)をもとにテーブルが作成される。確認してみる。

~~~bash
$ psql blogdev
blogdev=# \d posts
~~~

テーブルをドロップして確認してみる。
~~~bash
$ knex migrate:rollback
~~~

余談だけどデータベースの確認は`gui`ツールが便利だと思う。

## テストデータを作る
さっき作ったテーブルにテストデータを入力していく。`rollback`したままになっているなら`$ knex migrate:latest`しなおす。

### ファイル構造
新しいファイル構造は以下。

~~~
/
├── migrations....生成されたmigrationファイルが格納される
├── seeds.........生成されたseedファイルが格納される
├── knexfile.js...knexの設定ファイル
└── package.json
~~~

### シードファイルの作成
テストデータの作成は`knex`の`seed`を使う。シードファイルを作成。

~~~bash
knex seed:make posts
~~~

`posts`の部分は生成されるシードファイル名。任意のものを入れる。
`seeed/post.js`が生成される。

{% codeblock seed/post.js lang:javascript %}
exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex('table_name').del()
    .then(function () {
      return Promise.all([
        // Inserts seed entries
        knex('table_name').insert({id: 1, colName: 'rowValue1'}),
        knex('table_name').insert({id: 2, colName: 'rowValue2'}),
        knex('table_name').insert({id: 3, colName: 'rowValue3'})
      ]);
    });
};
{% endcodeblock %}

ここにテストデータを入れていく。

{% codeblock seed/post.js lang:javascript %}
exports.seed = function(knex, Promise) {
  const table = 'posts';
  return knex(table).del() // Deletes ALL existing entries
    .then(function() { // Inserts seed entries one by one in series
      return knex(table).insert({
        title: '昼食をとった',
        body: 'ラーメンとライスを食べた。美味しかった。',
        postDate:'2016-08-01 13:30:00'
      });
    }).then(function () {
      return knex(table).insert({
        title: '昼寝をした',
        body: '起きたら夜になった。悲しかった。',
        postDate:'2016-08-01 20:10:00'
      });
    }).then(function () {
      return knex(table).insert({
        title: '眠くならない',
        body: '眠れない。明日が辛い。',
        postDate:'2016-08-02 04:00:00'
      });
    });
};
{% endcodeblock %}

複数のデータを入れるのに`promise`を使っている。seedファイルを実行するたびに同じ順番でデータベースにはいる。

### シードファイルの実行
シードファイルを作ったら実行する。

~~~bash
$ knex seed:run
~~~

テーブルを空っぽにしてからデータが入る。確認してみよう。

## knexfile.jsの設定
上記の手順では、`seed`と`migrations`はデフォルトのディレクトリを使った。knexfile.jsから任意のディレクトリを指定することもできる。
また`test`と`development`のデータベースを分けたりもできる。

{% codeblock knexfile.js lang:javascript %}
module.exports = {
  test: {
    client: 'postgresql',
    connection: {
      database: 'blogtest'
    }
    migrations: {
      directory: '/db/migrations'
    },
    seeds: {
      directory: '/db/seeds/test'
    }
  },
  development: {
    client: 'postgresql',
    connection: {
      database: 'blogdev'
    }
    migrations: {
      directory: '/db/migrations'
    },
    seeds: {
      directory: '/db/seeds/dev'
    }
  }
};
{% endcodeblock %}


## 課題と疑問

* `$ knex seed:run`を繰り返すとIDがどんどん進んでいく。別にいいんだけど、なんかきもい。
* 複数のデータを`seed file`に入れるのに`then`を使うべきなのか。

## Refferences

* [knex.js](http://knexjs.org)
* [Test Driven Development With Node, Postgres, and Knex (Red/Green/Refactor)](http://mherman.org/blog/2016/04/28/test-driven-development-with-node)
* [【JavaScriptでもSQLしたい！】気がつくとそこはKnex.jsという名の天竺だった](http://qiita.com/YoshiyukiKato/items/59c9ac742536d706b322)
* [Knex, your SQL best friend.](http://www.dancorman.com/knex-your-sql-best-friend/)
