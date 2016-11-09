## exportとimportについて
### export
モジュールとして**使用される(呼び出される)側のファイル内で使用**し、「関数・変数・定数・クラスなどに対し、他のファイルからどれを利用可能にするか」を指定する。

### import
モジュールを**使用する(呼び出す)側で使用**し、「どのファイルを読み込んで使うか」「指定ファイル内のexportで指定されているものをどのようにバインドするか」を指定する。  
また、巻き上げ処理(ホスティング)があるためファイル末尾で使用しても先頭で宣言されたのと同じ状態になる。


## モジュール利用の流れ
(1)定義しようとするモジュール側で、外部に提供したい値の集合やオブジェクトにexport指定することで、値やオブジェクトを「返す」ように定義し、
(2)importの構文で指定された方法で、その「返された何か」を識別子に適切にバインドする。




### 構文
MDNから引用
```
▼import

//
1. import defaultMember from "module-name";

//まるごとインポート
2. import * as name from "module-name";

//ひとつだけインポート
3. import { member } from "module-name";

//モジュール名を変更してひとつだけインポート
4. import { member as alias } from "module-name";

//複数のものをインポート
5. import { member1 , member2 } from "module-name";

//一部のモジュール名を変更しつつ、複数のものをインポート
6. import { member1 , member2 as alias2 , [...] } from "module-name";

//
7. import defaultMember, { member [ , [...] ] } from "module-name";

//
8. import defaultMember, * as name from "module-name";

//
9. import "module-name";
```

### 使い方
#### 大前提
import,exportともにトップレベルで読み込む必要あり。  
以下のように関数内で実行しようとするとエラーになる。
```
(function(){
	import './modules/module01';
})();
```




#### 1. import defaultMember from "module-name";
モジュールの一つのメンバーのみインポート。myMember は現在のスコープになる。  

##### ソース
```
//<main.js>
import defaultMember from "./modules/module01";

↓↓↓

//<main.js>トランスパイル後
"use strict";

var _module = require("./modules/module01");

var _module2 = _interopRequireDefault(_module);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
```



#### 2. import * as myModule from "my-module";
##### ソース
```
//<main.js>
import * as myModule from "./modules/module01";

↓↓↓

//<main.js>トランスパイル後
"use strict";

var _module = require("./modules/module01");

var myModule = _interopRequireWildcard(_module);

function _interopRequireWildcard(obj) { if (obj && obj.__esModule) { return obj; } else { var newObj = {}; if (obj != null) { for (var key in obj) { if (Object.prototype.hasOwnProperty.call(obj, key)) newObj[key] = obj[key]; } } newObj.default = obj; return newObj; } }
```



#### 3. import {myMember} from "./modules/module01";
##### ソース
```
//<main.js>

↓↓↓

//<main.js>トランスパイル後

```





#### 9. import "module-name";
どの名前にもバインドせずに全モジュールをインポートする場合。

##### ソース
```
//<main.js>
import './modules/module01';

```

##### トランスパイル&出力結果
```
//main.js
import './modules/module01';
```
↓Babelでのトランスパイル後
```
//main.js
'use strict';

require('./modules/module01');
```
↓browserifyでのコンパイル後のソースは省略
```
//ブラウザのコンソール
1
2
```



### 結論
・読み込んだモジュールの書き換えはできない




## 実案件で使えるかも
例えばjQueryやそれに依存したライブラリなんかを使う場合、ファイルの読み込み順に気を配りつつ、HTML側で各JSファイルを読み込む必要がある。
もしHTML側で1つのJSファイルのみ読み込んで、依存関係なんかも勝手に上手いこと処理してくれると素敵(?)



## やってみた
### フォルダ構造
```
js
├── bundle.js・・・メインとモジュールをbrowserifyで連結したもの
├── function.js・・・メイン
└── modules
    └── module01.js・・・モジュール単位で分割したファイル
```



## 現状使えるブラウザはない
ES2015の「ES Modules」と呼ばれるモジュール機構は**現状どのブラウザでも動きません。**

その理由は、**「そのファイルが何を依存として持つかそのソースをパースするまで不明」** という事情があります。パフォーマンスを度外視するなら、一つずつファイルを落としてクライアントで評価し、その結果またリクエストを発行し…と繰り返せばいつか終わりますが、大量に分割されたファイルでそれをやるのは現実的ではありません。

そのため、事前に開発環境でJSを静的解析し、くっつけてしまったものを配布するのが主流のようです。






ES Modules の import/export 宣言は、そのファイルスコープのトップレベルで、必ず静的に(変数を使わずに)行う必要があります。

トップレベルの箇所に変数や関数を置いてしまうと
「グローバルオブジェクト」への代入となってしまい、
プログラムのどこからでもアクセス(呼び出しや変更)が出来てしまいます。
また、勘違いしがちなのが、別のjsファイルに書いたからといって、
別々のスコープが生まれる訳ではないということです。





------------------------------------------------------
importとexportについて
------------------------------------------------------
ざっくり言うとJSファイル間での読み込みを可能にし、HTML側で読み込むファイルは一つだけど必要に応じてモジュール化されたJSファイルを読むといったことが容易になるっぽい。

そもそも、JavaScriptにはコードをモジュール化してそれを必要な際に呼び出して使用するような仕組みが言語仕様として存在しない。

よほど小さいプログラムでも無い限り全ての処理を1ファイルにまとめたいという強い意志を持ち続けられる人はそう多くないはずだ。たいていの人は処理毎とかにファイルを分割して、必要な際に必要なファイルを呼び出して使う、という方針で開発を進めたいと思うものだと思ってる。
（スライダーなど、必要なページにのみ読み込ませて使いたい）

じゃあどうやって複数のファイルにまたがった処理を書くんだよ！ってなるんだけれども、それをブラウザ環境ではグローバルオブジェクト（ブラウザなら window オブジェクト）にプロパティを作って、それを使い回すという割と強引な手法が長らく使われてきた。

ただ、サーバサイドとしての JavaScript である NodeJS ではその辺が解消されていて、require という関数を使えば外部ファイルを読み込めるようになっている。

その羨ましい手法を使えるようにしたのが Browserify。Browserify で JS をビルドすれば NodeJS のように requre が使えて、モジュールを分割した書き方が出来るようになる。これでもう尋常じゃない数のスクリプトタグや gulpfile 内に何個も何個もモジュールを書き足してその度に gulp コマンドを再実行することがなくなる。

## import
### importとは
import 文 は、外部モジュールや他のスクリプトなどからエクスポートされた関数、オブジェクト、プリミティブをインポートするために使用します。(MDNから引用)

### 構文
```
import defaultMember from "module-name";
import * as name from "module-name";
import { member } from "module-name";
import { member as alias } from "module-name";
import { member1 , member2 } from "module-name";
import { member1 , member2 as alias2 , [...] } from "module-name";
import defaultMember, { member [ , [...] ] } from "module-name";
import defaultMember, * as name from "module-name";
import "module-name";
```


import defaultMember from "./module-name";

拡張子「.js」は省略可

import './underscore';

※ファイルを丸ごとインポートすることで、そのファイルのトップレベルで全てのコードを実行するということに注意してください。

名前付きインポート
import { sumTwo, sumThree } from 'math/addition';

名前付きインポートの名前変更
import { sumTwo as addTwoNumbers, sumThree as sumThreeNumbers} from 'math/addition';

それに加え、全てをインポートすることもできます（名前空間インポートと呼ばれることもあります）。

import * as util from 'math/addition';

エクスポートされた値は参照ではなく、バインディングです。ですから、あるモジュール内で値のバインディングを変更する時は、エクスポートされたモジュール内の値に影響を及ぼします。これらのエクスポートされた値のパブリックインターフェースは変えないようにしましょう。


・default exportは名前わからなくてもいいので使いやすそう
・named exportは名前わからないと使えない
・default exportが重複する場合はエラー？
・存在しないモジュールの読み込みは当然エラー？
・存在しないバインディングのimportも当然エラー？
・defaultとnamedのプロパティは別物
・moduleの場合強制strictモード？
・トップレベルthisはundefined(非window)？
・トップレベル変数がグローパルにならない？

## export
### importとは
export 文は、指定したファイル (またはモジュール) から関数、オブジェクト、プリミティブをエクスポートするために使用します。(MDNから引用)

### 構文
```
export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // var も使用可
export let name1 = …, name2 = …, …, nameN; // var, const も使用可

export default expression;
export default function (…) { … } // class, function* も使用可
export default function name1(…) { … } // class, function* も使用可
export { name1 as default, … };

export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```

・export方法は「デフォルトエクスポート(default export)」と「名前付きエクスポート(named export)」の2種類ある

名前付きエクスポート
export let name = 'David';
export let age = 25;

複数のオブジェクトもエクスポート可能
function sumTwo(a, b) { return a + b;}
function sumThree(a, b, c) { return a + b + c;}
export { sumTwo, sumThree };

関数もエクスポート可能
export function sumTwo(a, b) { return a + b;}
export function sumThree(a, b, c) { return a + b + c;}

ベストプラクティス：常にexport defaultメソッドを、モジュールの最後で使ってください。これにより、何がエクスポートされるのかを明らかにし、何という名前でエクスポートされるのかをはっきりさせることで時間を節約します。さらに、CommonJSモジュールにおける一般的な慣習は、1つの値やオブジェクトをエクスポートすることです。このパラダイムを順守することで、コードを簡単に読めるようにし、私たち自身がCommonJSとES6モジュールの間に入り込めるようにするのです。



## モジュール利用の流れ
(1)定義しようとするモジュール側で、外部に提供したい値の集合やオブジェクトにexport指定することで、値やオブジェクトを「返す」ように定義し、
(2)importの構文で指定された方法で、その「返された何か」を識別子に適切にバインドする。

(1)の段階でのモジュールからの値の返し方には、以下の3つがある。

1. named export: 複数値をobjectで返す
2. default export: 単一値を返す
3. named exportと, default exportの混在: 単一値と複数値の両方を返す


ただ、残念ながらまだブラウザでは使うことができません。
例えば以下のようなコードがあった場合に、
```
import React from "react"
```
Chromeで実行させると以下のようにエラーが出て使えません。（当然ですね）
```
Uncaught SyntaxError: Unexpected token import
```

babelでトランスパイルすれば使えるでしょ。
(例)
トランスパイル前
```
export function hello () {
    console.log("Hello from a.js");
};
```
↓↓↓
トランスパイル後
```
"use strict";

Object.defineProperty(exports, "__esModule", {
    value: true
});
exports.hello = hello;
function hello() {
    console.log("Hello from a.js");
};
```

babel
https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact%2Cstage-2&code=



上記の通りexportsとrequireを用いた方式（Nodeの方式）にコンパイルされただけでブラウザで使おうとすると以下のようなエラーが出る。
```
Uncaught ReferenceError: require is not defined
```
つまりまだ使えません／(^o^)＼

じゃあ、どうするか

ブラウザでes2015のimportとexportを使う方法はいくつかあるそうですが、
手軽っぽかったので「Browserify × babel」で使ってみました。

### Browserifyとは
nodeのrequireやmodule.exportsを使ってモジュール管理ができる仕組み。また一部のnodeモジュールもフロントエンドで利用できるようになる。Browserifyを使うことで、モジュール分割して開発したコードを、最終的に1つのJSファイルにコンパイルすることができる。

他にも似たようなものでWebPackがあるが、WebPackはCSSや画像など含めフロントエンド全般に関するツールで、BrowserifyはJavaScriptのモジュール管理に特化したツールです。

Browserifyのインストール
```
 sudo npm i -g browserify
```

流れ
es2015　→→(babelでトランスパイル)→→　node　→→(browserifyでコンパイル)→→　ブラウザで使用可能な状態に！

### 参考記事

**▼browserifyの設定**  
gulp で browserify - 新しい日記　http://tttttahiti.hatenablog.com/entry/2015/08/18/185325  


【古い記事】Babelで理解するEcmaScript6の import / export
http://qiita.com/inuscript/items/41168a50904242005271

ES6 チートシート
http://postd.cc/es6-cheatsheet/#modules

[フロントエンド] ES2015のimport/exportをブラウザで使うためのコンパイル（Babel × Browserify）
http://www.yoheim.net/blog.php?q=20160804
