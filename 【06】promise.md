# promiseについて

## promise（ES6 Promises）とは？
- 非同期処理を抽象化したオブジェクト、非同期処理を扱うためのAPI
例）
```
var promise = new Promise(function(resolve, reject){});
promise.then(function(result){
  // success時の処理
}).catch(function(error){
  // error時の処理
});
```

### コールバックによる非同期処理との違い
- コードの書き方が決まっている
- 常に非同期処理が行われる = 同期・非同期処理の混在による問題が起きない
  
### promiseに定義されたAPI
1. コンストラクタ
2. インスタンスメソッド
3. スタティックメソッド

（１）コンストラクタ
= promiseは、コンストラクタからインスタンスとなるオブジェクトを生成して処理している。
```
var promise = new Promise(function(resolve, reject){})
  // promise : オブジェクト
  // Promise : コンストラクタ
```

（２）インスタンスメソッド
= コールバック関数を登録するためのメソッド。主なものは .then() と .catch()。
```
promise.then(onFulfilled, onRejected)
// resolve（成功）した時 → onFulfilled
// reject（失敗）した時 → onRejected

promise.catch(onRejected)
promise.then(undefined, onRejected)
// reject（失敗）した時のみ登録
// .catch()はIE8以下未対応（["catch"]を使えば使える）
```

▲ （１）と（２）をまとめると
```
var promise = new Promise(function(resolve, reject){})

promise.then(function onFulfilled(value){
  // 実行したい処理
}).catch(function onRejected(error){
  // エラー処理
});
```

（３）スタティックメソッド
= promiseが持つ静的メソッド
例）Promise.all()　Promise.resolve()　など
  
### promiseの状態
1. Fulfilled = resolveした時
2. Rejected = rejectした時
3. Pending = 初期状態（1,2のどちらでもない）

▲ promiseオブジェクトの状態がPendingからFulfilled/Rejectedに変化した時に呼ばれる関数を登録するのが.then()メソッド。つまり、実行されるのは一度だけ。
  
## promiseの書き方
```
var promise = new Promise(function(resolve){
  resolve(111);
});
promise.then(function(value){
  console.log(value); // 111
});
```
  
### Promise.resolve()
- 上記記述をショートカット
```
Promise.resolve(111).then(function(value){
  console.log(value);
});
```

- thenableなオブジェクトをpromiseオブジェクトに変換  
（thenable = .thenというメソッドを持っているオブジェクト）
```
Promise.resolve($.ajax({
  // ajax処理
}).then(function(value){
  console.log(value);
});
```
▲ jQuery.ajax()はDeferred Objectのメソッドで.then()が使える  
  Promise.resolve()によってpromiseオブジェクトに変換することで、ES6 Promisesの機能を使えるようにしている
  
### Promise.reject()
```
var promise = new Promise(function(resolve, reject){
  reject(new Error('エラー'));
});
promise.catch(function(error){
  console.error(error);
});

↓

Promise.reject(new Error('エラー')).catch(function(error){
  console.error(error);
});
```
  
### promiseのメソッドチェーン
* 順番
```
function taskA(){
  console.log('A');
}
function taskB(){
  console.log('B');
}
function taskC(){
  console.log('C');
}
function onRejected(error){
  console.log('ERROR');
}

var promise = Promise.resolve();
promise
  .then(taskA)  // success=taskB, error=onRejected
  .then(taskB)  // success=taskC, error=onRejected
  .catch(onRejected)
  .then(taskC);  // success=完了, error=キャッチしてもらえない
```

* 値渡し
```
function taskA(value){
  return value + 1;
}
function taskB(value){
  return value * 2;
}
function taskC(value){
  console.log(value);
}

var promise = Promise.resolve(1);
promise
  .then(taskA)
  .then(taskB)
  .then(taskC)
  .catch(function(error){
    console.error(error);
  });
```
  
## promiseと配列
Promise.allとPromise.raceは、promiseオブジェクトの配列を受け取り、  
その配列に入っているpromiseオブジェクト全てを見て次の処理を実行することができる。
  
### Promise.all
= promiseオブジェクト配列のオブジェクトが、すべてFulfilledになったら.then()を実行する。

```
function prTimer(delay){
  return new Promise(function(resolve){
    setTimeout(function(){
      resolve(delay);
      console.log(delay);
    }, delay);
  });
}

Promise.all([
  prTimer(100),
  prTimer(200),
  prTimer(300),
  prTimer(400),
  prTimer(500)
]).then(function(value){
  console.log('result = ' + value);
});
```
  
### Promise.race
= promiseオブジェクト配列のオブジェクトが、ひとつでもFulfilledになったら.then()を実行する。

```
function prTimer(delay){
  return new Promise(function(resolve){
    setTimeout(function(){
      resolve(delay);
      console.log(delay);
    }, delay);
  });
}

Promise.race([
  prTimer(100),
  prTimer(200),
  prTimer(300),
  prTimer(400),
  prTimer(500)
]).then(function(value){
  console.log('result = ' + value);
});
```
  
## エラー処理として使うなら、then()？catch()？
```
promise.catch() == promise.then(undefined, onRejected)
```
上記2つは同じ意味。
しかし、.catch()の方が推奨される。それはなぜか？

→ .then(undefined, onRejected)でエラー処理ができないことがある

```
function throwError(value){
  throw new Error(value);
}

function taskA(onRejected){
  return Promise.resolve(777).then(throwError, onRejected);
}

function taskB(onRejected){
  return Promise.resolve(777).then(throwError).catch(onRejected);
}

taskA(function(){
  console.log('A');
});
taskB(function(){
  console.log('B');
});
```
* taskA → 第一因数（onFulfilled）内でエラーが起きても、onRejectedに行かずエラー処理が動かない
* taskB → .then(throwError)内で起きたエラーをキャッチできる
  
■ 参考文献  
-　-　-　-　-　-　-　-　-　-　-  
[JavaScript Promiseの本](http://azu.github.io/promises-book/)  
[今更だけどPromise入門](http://qiita.com/koki_cheese/items/c559da338a3d307c9d88)  
