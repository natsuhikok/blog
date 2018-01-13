---
title: mochaでユニットテストを書く
tags:
- mocha
- node.js
description: mochaを使ってjavascriptをテストする。インストールからテストコード書くとこまで。
date:
---
`mocha`を使って`javascript`をテストする。インストールからテストコード書くとこまで。

## install
```bash
$ npm install -D mocha
# or
$ npm install --global mocha
```

## 基本的な使い方
* テスト対象のコードを`require`する
* `$ mocha` で`./test/*.js`のコードをテストする。グローバルインストール時のみ？
* describe > it
```javascript
const func = require('./functions.js');
describe('計算', function() {
    const x = 3;
    const y = 2;
    const messageError = '誤答';
    // 足し算
    it('3 + 2', function() {
        if (func.tasu(x,y) === 5) {
        } else {
          throw new Error(messageError);
        }
    });
    // 引き算
    it('3 - 2', function() {
        if (func.hiku(x,y) === 1) {
        } else {
          throw new Error(messageError);
        }
    });
    // 掛け算
    it('3 * 2', function() {
        if (func.kakeru(x,y) === 6) {
        } else {
          throw new Error(messageError);
        }
    });
});
```

## APIをテストする
`chai`と`chai-http`を使ってAPIのテストをする。

```bash
$ npm install --save-div chai chai-http
```

```javascript
const chai = require('chai');
const chaiHttp = require('chai-http');
const should = chai.should();

chai.use(chaiHttp);
describe('Routing', function() {
  const url = 'http://localhost:3000';
  it('should a message on / GET"', function(done) {
  chai.request(url)
    .get('/')
    .end(function(err, res){
      res.body.message.should.equal('This server is running.');
      done();
    });
  });
  it('up comming test');
});
```

## reference
* [Mocha - the fun, simple, flexible JavaScript test framework](https://mochajs.org)
* [Testing Node.js With Mocha and Chai](http://mherman.org/blog/2015/09/10/testing-node-js-with-mocha-and-chai)
* [chaijs/chai-http](https://github.com/chaijs/chai-http)
