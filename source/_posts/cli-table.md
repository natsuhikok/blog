---
title: cli-tableでcliテーブルを作る
tags:
- node.js
- cli
description: コマンドラインにテーブルを表示するcli-tableを使う。
date:
---

`cli-table`はコマンドラインにテーブルを表示する`node module`。Webでビューを作るほどでもないけど、インラインの`console.log`だと情報量不足ってときに便利。

## install
cli-table: Pretty unicode tables for the CLI - [https://www.npmjs.com/package/cli-table](https://www.npmjs.com/package/cli-table)
```bash
$ npm install cli-table
```

## code example

```js
const Table = require('cli-table');
//////////////////////////////////////
// Horizontal Table
//////////////////////////////////////
const tableHorizontal = new Table({
    head: ['TH 1 label', 'TH 2 label']
  });

// table is an Array
tableHorizontal.push(
  ['First value', 'Second value'],
  ['First value', 'Second value']
);

console.log(tableHorizontal.toString());

//////////////////////////////////////
// Vertical table
//////////////////////////////////////
const tableVertical = new Table();
tableVertical.push(
    { 'Some key': 'Some value' },
    { 'Another key': 'Another value' }
);

console.log(tableVertical.toString());

//////////////////////////////////////
// Cross Table
//////////////////////////////////////
const tableCross = new Table({
  head: ["", "Top Header 1", "Top Header 2"]
});
tableCross.push(
    {
      'Left Header 1': ['Value Row 1 Col 1', 'Value Row 1 Col 2']
    },
    {
      'Left Header 2': ['Value Row 2 Col 1', 'Value Row 2 Col 2']
    }
);

console.log(tableCross.toString());
```
