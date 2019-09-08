---
title: jsのasync/awaitでrejectしたいときはthrow. これ大事.
date: 2019-00-00 00:00:00
categories:
    - [プログラミング言語]
tags:
    - [javascript]
    - [async/await]
    - [promise]
---

## 結論
jsのasync/awaitを利用している時, Promiseのreject相当の動きをしたいときはthrowを使う.

### Promiseを利用した場合
```js
function getUserBy(id) {
  return new Promise((resolve, reject) => {
    db.getUser(id).then(user => {
      // userを利用したなにかの処理
      resolve(user);
    }).catch(e => {
      reject(e);
    });
  });
}

```

### async/awaitを利用した場合
```js
async function getUserBy(id) {
  const user = await db.getUser(id).catch(e => throw new Error(e));
  // userを利用したなにかの処理
  return user;
}

```

## あらすじ(throwを知らない場合)

[async関数においてtry/catchではなくawait/catchパターンを活用する - Qiita](https://qiita.com/akameco/items/cc73afcdb5ac5d0774bc) の記事などでasync関数を利用する際はawait/catchで書くとスマートに書けるよと紹介してある. これに感銘を受けて私もよく利用していたのだがasync関数内でのthrowの働きを知らなかったためにやりたいことができないことが多々あった. 

例えば, ユーザー情報をデータベースから取得する場合を考えてみる.
ユーザー情報を取得しそのユーザーをもとになにか処理を行う場合async関数を使うと次のように書ける.

```js
async function getUserBy(id) {
  const user = await db.getUser(id);
  // ユーザー情報を利用したなにかの処理
  return user;
}

```

もし, ユーザーが取得できなかったら処理を中断したい場合にawait/catchを利用しようとするとthrowなしではうまくいかない.

```js
async function getUserBy(id) {
  // userがなかった場合, 関数からreturnしたいが...
  const user = await db.getUser(id).catch(() => {
    // ここでreturnしてもuser変数の中に戻り値が格納されてしまうだけ
    return;
  });
  // ユーザー情報がないためユーザー情報を利用したなにかの処理でエラーが発生する可能性
  return user;
}
```

また, try/catchを利用してもthrowを使わないとかなり冗長的になってしまう.

```js
async function getUserBy(id) {
  try {
    const user = await db.getUser(id);
    // 他のtry/catchもここに入る可能性
    return user;
  } catch() {
    // 何もしない?
  }
}
```

## throw文の登場

ここで登場するのが, jsの`throw`.
[MDNのthrowの説明](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/throw)を見てみると,

> **`throw` 文**は、ユーザー定義の例外を投げます。現在の関数の実行を止めて (`throw` の後の文は実行しません)、コールスタック内の最初の [`catch`](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/try...catch) ブロックに制御を移します。呼び出し元の関数に `catch` ブロックが存在しない場合は、プログラムが終了します。

とある. 実は今までろくにthrow文を使ったことがなかったのでよく仕様について理解できていなかったのだが, throwはreturnと同じく関数の実行をその場で止めるため, あらすじであったようなユーザー情報が取得できなかったら関数の処理を止めたいなどのユースケースに役に立つ. 

(ちなみに今までの私のエラーハンドリングはもっぱらこんな感じ:
```js
fs.readFile('/etc/passwd', (err, data) => {
  if (err) {
    return false;
  }
  
  // dataを使った処理
});
```

## new Error()も一緒に使おう

また, 色々と調べてみるとこちらのブログ([JavaScript で throw “” ではなく throw new Error() を使ったほうがよい(些細な)理由 - latest log](http://uupaa.hatenablog.com/entry/2014/03/12/050707)) で書いてあるようにthrowを使うときは, Errorオブジェクトを返すのがいいらしいです.

```js
throw new Error("This is error message");

```


今までそれすらも知らずによくjsを書けてたなって自分でも思いますが, それでもいい感じに動いちゃうのがjsらしいですね.