---
title: postgreSQL with knex.js (2) - queries
tags:
- express.js
- node.js
- knex.js
- postgreSQL
description: expressでknex.jsのクエリビルダーを使う。
date: 2016-08-07
---
[postgreSQL with knex.js (1) - migrate and seed](http://hikonaz.com/2016/08/knex_migration_seed/)の続き。expressでknex.jsのクエリビルダーを使う。

## ファイル構造
全体のファイル構造はこんなかんじ。

~~~
/
├── migrations
│    └── timestamp_blog.js
├── seeds
│    └── posts.js
├── views
│    ├── index.jade
│    ├── post.jade
│    └── posted.jade
├── app.js
├── db.js
├── queries.js
├── knexfile.js
└── package.json
~~~

## express.js
`express.js`の準備する。テンプレートエンジンは、`jade`を使うので`express`と`jade`をインストールする。

~~~bash
$ npm install --save express jade
~~~

まずは`express`が動くところまで作る。

{% codeblock app.js lang:javascript %}
var express = require('express');
var app = express();
var port = 4000;

// views
app.set('views', './views');
app.set('view engine', 'jade');

///////////////////////////////////
// index.page
///////////////////////////////////
app.get('/', function (req, res) {
  res.render(
    'index',
    {
      title : 'express!'
    }
  );
});

app.listen(port, function () {
  console.log("Listening on port", port);
});
{% endcodeblock %}

{% codeblock views/index.jade lang:jade %}
doctype html
html
  head
    title= title
  body
    h1= title
    p Welcome to #{title}
{% endcodeblock %}

これで、viewとappの準備ができたので、express serverを起動してみる。

~~~bash
$ node app.js
~~~

[http://localhost:4000/](http://localhost:4000/)にアクセスすると`Welcome to express!`がでてくるはず。

## GET all posts
knexのQuery Builderを使って、`express`から`postgreSQL`にアクセスして`post`テーブルのデータを取得する。
途中で気づいたのだけれど、GET/POSTに対して、postテーブル分かりづらい・・。

データベースの設定をする。

{% codeblock db.js lang:javascript %}
var config      = require('./knexfile.js');
var env         = 'development';
var knex        = require('knex')(config[env]);
module.exports = knex;
{% endcodeblock %}

queriesを作る。
{% codeblock queries.js lang:javascript %}
var knex = require('./db.js');

function posts() {
  return knex('posts');
}

//////////////////////////
// queries
//////////////////////////
var queries = {
  getAllPosts: function () {
    return posts().select();
  },
}
module.exports = queries;
{% endcodeblock %}

app.jsを編集して、indexページにアクセスするとconsoleにデータベースから受け取ったデータを表示できるようにする。

{% codeblock app.js lang:javascript %}
var express = require('express');
var app     = express();
var queries = require('./queries.js');

var port = 4000;

...(中略)

///////////////////////////////////
// index page
///////////////////////////////////
app.get('/', function (req, res) {
  queries.getAllPosts()
  .then(function(posts) {
    res.render(
      'index',
      {
        title : 'express!',
        posts : 'post',
      }
    )
    console.log(posts);
  });
});
...
{% endcodeblock %}

サーバーを起動してindex pageにアクセスしてターミナル(ブラウザのコンソールじゃなくてターミナル)を確認。データベースの全記事が出力されていることを確認する。

正しく出力されていることを確認したらviewに表示できるようにする。
{% codeblock views/index.jade lang:jade %}
doctype html
html
  head
    title= title
  body
    - each key in posts
      ul
        li #{key.title}
        li #{key.body}
        li #{key.postDate}
{% endcodeblock %}

サーバーを起動してindex pageにアクセスすると取得したデータが表示されているはず。

## GET single post
次は、個別記事ページを作る。全体記事から個別記事へのリンクがあるかんじ。
idから個別記事を取得するクエリを追加。

{% codeblock queries.js lang:javascript %}
//////////////////////////
// queries
//////////////////////////
var queries = {
  getAllPosts: function () {
    return posts().select();
  },
  getPost: function(postID) {
    return posts().where('id', parseInt(postID)).first();
  }
}
{% endcodeblock %}

個別記事ページを作る。

{% codeblock app.js lang:javascript %}
...
///////////////////////////////////
// single posts
///////////////////////////////////
app.get('/posts/:id', function (req, res) {
  queries.getPost(req.params.id)
  .then(function(post) {
    res.render(
      'post',
      {
        post : post,
      }
    )
  })
});
...
{% endcodeblock %}

viewを編集する。全体記事から個別記事へのリンクを貼る。`<a href="./posts/[id]"></a>`で`<ul></ul>`全体を囲うだけ。

{% codeblock views/index.jade lang:jade %}
doctype html
html
  head
    title= title
  body
    - each post in posts
      a(href="./posts/#{post.id}")
        ul
          li #{post.title}
          li #{post.body}
          li #{post.postDate}
{% endcodeblock %}

個別記事ページのview。
{% codeblock views/post.jade lang:jade %}
doctype html
html
  head
    title #{post.title}
  body
    h1 #{post.title}
    p #{post.body}
    p #{post.postDate}
{% endcodeblock %}

サーバーを起動してindex pageにアクセスすると各記事がリンクになっているのでクリックするとそれぞれの個別ページに移動する。


## POST post
記事を投稿する。POSTを受け取るには`body-parser`がいるので、これをインストールする。

~~~bash
npm install --save body-parser
~~~

app.jsから`body-parser`を読み込む。

{% codeblock app.js lang:javascript %}
var express = require('express');
var app     = express();
var queries = require('./queries.js');

var port = 4000;

var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended: true}));
...
{% endcodeblock %}

記事を追加するクエリを追加する。
{% codeblock queries.js lang:javascript %}
//////////////////////////
// queries
//////////////////////////
var queries = {
  getAllPosts: function () {
    return posts().select();
  },
  getPost: function(postID) {
    return posts().where('id', parseInt(postID)).first();
  },
  addPost: function(post) {
    return posts().insert(post, 'id');
  }
}
{% endcodeblock %}

app.jsにpostを追加する。'/posts'が受け取ってデータベースに追加する。追加したら新しく追加した記事を表示する。

データベースのフィールドは`id`,`title`,`body`,`postDate`の３つ。`id`は自動、'title','body'はフォームからの入力。postDateは受け取ったあとapp.js内で追加してからデータベースに入れる。

日付を扱うモジュールをインストールする。
~~~bash
npm install date-utils --save
~~~

{% codeblock app.js lang:javascript %}
///////////////////////////////////
// Post post
///////////////////////////////////
app.post('/posts', function(req, res) {
  require('date-utils');
  var newPost = req.body;
  newPost.postDate = new Date();
  queries.addPost(newPost)
  .then(function(postID) {
    return queries.getPost(postID);
  })
  .then(function(post) {
    res.render(
      'posted',
      {
        post : post,
      }
    )
  });
});
{% endcodeblock %}

index pageにフォームを追加する。
{% codeblock views/index.jade lang:jade %}
doctype html
html
  head
    title= title
  body
    form(method="POST", action="./posts",target="_self")
      span
        | title
        input(type="text", name="title" value="title")
      span
        | content
        input(type="text", name="body" value="body")
      input(type="submit", value="送信")
    - each post in posts
      a(href="./posts/#{post.id}")
        ul
          li #{post.title}
          li #{post.body}
          li #{post.postDate}
{% endcodeblock %}

POST完了後に表示するviewも作る。
{% codeblock views/posted.jade lang:jade %}
doctype html
html
  head
    title #{post.title}
  body
    p posted!
    h1 #{post.title}
    p #{post.body}
    p #{post.postDate}
{% endcodeblock %}

サーバーを起動して、index pageのフォームを入力して送信するとデータベースにデータが追加されるはず。

## 参考
* [knex.js](http://knexjs.org)
* [Test Driven Development With Node, Postgres, and Knex (Red/Green/Refactor)](http://mherman.org/blog/2016/04/28/test-driven-development-with-node/)
* [express 4.x API](https://expressjs.com/en/4x/api.html)
* [Express Routing](http://expressjs.com/en/guide/routing.html)
* [PostgreSQL: Documentation: 9.1: Date/Time Types](https://www.postgresql.org/docs/9.1/static/datatype-datetime.html)
* [【JavaScriptでもSQLしたい！】気がつくとそこはKnex.jsという名の天竺だった](http://qiita.com/YoshiyukiKato/items/59c9ac742536d706b322)
* [ExpressでPOSTを処理するメモ](http://qiita.com/K_ichi/items/c70bf4b08467717460d5)
