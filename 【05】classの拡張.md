# classの拡張について

## classとは？の前に、オブジェクトとは？
- データと処理の集まり・ひとつのテーマを持った集まり
例）Userクラス  
プロパティ（状態）→名前、住所、電話番号...  
メソッド（classに対する振る舞い・命令）→名前を返す、住所を振り分ける...  
→**『データを元にクラスからインスタンスを生成し、インスタンスはそのデータを元にして仕事をする』**
というのがオブジェクト指向の基本形の考え方

## オブジェクトの3大特徴
1. クラス
2. ポリモーフィズム
3. 継承

## クラスでプログラムを作る3大メリット
### 1) まとめる（機能ごとにメソッドをまとめられる）
1. 大きな箱にごちゃっと入れるより、CD・雑誌・文房具のように小分けに分類した方があとからわかりやすいし探しやすい。
2. classで作ればメソッド名を重複させても大丈夫。プログラム全体でユニークな関数名じゃなくてもopenとかいう簡単なメソッド名でもOKになる。→同じ関数名があっても、親のクラスが違えば違うものなので。（地味にうれしいメリット）

```
//同じopenメソッドでも親クラスが違うので、違うメソッド
class AObj {
  open() {
    //処理
  }
}

class BObj {
  open() {
    //処理
  }
}
```
classを使用する以前は、変数や関数がただ散らばっていてどの変数がどこの関数に所属しているのか、というのがわからない状態だった。  
→でもこれって今のJSでは？  


### 2) 隠せる
クラスの中に定義しても、グローバル変数のままでは他で上書きされる恐れがある。
```
//以下説明のためtypescriptの書き方
class AObj {
  private hensu: string;
}

```
privateを使うことで、クラス内からしかアクセスできずグローバル汚染を防ぐことができる。
なので、変数をprivateにしておいて、取得はゲッターメソッドを作るとかにするとかできる。
> - public => どこからでもアクセス可能
> - protected => クラス内、同一パッケージ、サブクラスからアクセス可（privateとpublicのいいとこ取り！）
> - private => クラス内のみアクセス可能
>
> つまり，privateは自分からしか見えないメソッドであるのに対して，protectedは一般の人からは見られたくないが，仲間(クラスが同じオブジェクト)からは見えるメソッドです．
protectedは例えば2項演算子の実装にもう一方のオブジェクトの状態を知る必要があるか調べる必要があるが，そのメソッドをpublicにして，広く公開するのは避けたいというような時に使います．  

ちなみにtypescriptならpublic/private/protectedを使用できます。（es6では検討中らしい？）
private変数は「アンダースコア」を付けて定義するのがgoogle jsスタイルガイド流。  

### 3) たくさん作れる
クラスを作る時点で、インスタンスのことは考えなくてもいい。  
クラス内で何か値保持用として変数を一つ定義したとします。→インスタンスしたとき値保持用の変数は2つ必要？  
→いいえ、インスタンスを作った時点でインスタンス用のメモリが用意されるので問題ないです。  
そのインスタンスを代入した変数に対して操作すればOK。
```
class AObj {
  var hensu = null;
  open (){

  }
}
var ins = new AObj();
ins.open();
//インスタンスに対して操作。インスタンス用のメモリが確保されている。
```



## classとは↑オブジェクトの設計書
- 家の設計図→class
- 実際に建てた家→インスタンス
※設計図はドアは開けられないけど、インスタンスは実際に建っているのでドアが開けられる
→つまり、インスタンス化（実体化）させないとclass作っても使えないよ！ということ。

ただし例外もあります。＝静的メソッドと呼ばれるもの。  
これはclassに直接アクセスして使用できる仕様にもできる。（ふふ...



■参考
[google style guide 日本語訳](https://www38.atwiki.jp/aias-jsstyleguide2/pages/13.html)  
[google js styleguide](https://google.github.io/styleguide/javascriptguide.xml)  
[スタイルガイドや命名規則記事まとめ](http://qiita.com/kitsuki00/items/79428876391d0179a2ca)  



## ポリモーフィズムについて
ポリモーフィズム=***呼び出す側のコードを共通化する技術***  
→同じメソッドで、呼び出すオブジェクトによって違うものを実行出来る仕組みのこと。
> 「鳴く」というメソッドを呼び出したら  
> 犬「ワン」・猫「にゃー」・羊「め〜」とそれぞれに応じた返り値がくること  

→メソッドの部分は、共通して使える形にしないとだめ。（一つだけ引数だ必要とかそういうのだめ）  
→それが継承と結びつく。継承はポリモーフィズムでまとめてさらに、似たようなクラスの共通部分をまとめること。
```
class Animal {
  constructor(fuga){
    this.fuga = fuga;
    //メンバ変数はthis.プロパティ名で指定。（thisは省略不可）
  }
  call() {
    alert('わんわん');
  }
}
var dog = new Animal();
//呼び出すときはnew演算子を使用。
dog.call();//わんわん！

var cat = new Animal();
cat.call();//にゃーとはならない。
```


## 継承について
→***クラスの共通部分を別クラスにまとめる仕組み***
```
class Dog {
  priavte string:kind;
  constructor(fuga){
    this.fuga = fuga;
    //メンバ変数はthis.プロパティ名で指定。（thisは省略不可）
  }
  call() {
    alert('わんわん');
  }
}
class Cat {
  priavte string:kind;
  constructor(fuga){
    this.fuga = fuga;
    //メンバ変数はthis.プロパティ名で指定。（thisは省略不可）
  }
  call() {
    alert('にゃー');
  }
}
class Sheep {
  priavte string:kind;
  constructor(fuga){
    this.fuga = fuga;
    //メンバ変数はthis.プロパティ名で指定。（thisは省略不可）
  }
  call() {
    alert('め〜');
  }
}

```  

↑で違うのはcallメソッドのみ。それ以外は一緒だからまとめた方がいいよね！  


### 継承を使う意味
- 何回も似たようなclassを作るのは非効率的。  
- メソッドやプロパティを親クラスにまとめることが出来るので見通しが良くなる  
- 共通部分を親クラスに定義しておき、必要に応じて、差分があれば子クラスでオーバーライドできる
継承することで、自分で定義しなくても親クラスのプロパティやメソッドを使えるようになる。

```
class Hoge {
  constructor(fuga){
    this.fuga = fuga;
    //メンバ変数はthis.プロパティ名で指定。（thisは省略不可）
  }
  piyo() {
    alert(this.fuga);
  }
}
var hogetan = new Hoge("FUGA");
//呼び出すときはnew演算子を使用。
hogetan.piyo();
```
## constructorとは？
インスタンスが作られたときに自動的に最初に実行されるメソッド。  
constructorに何も値を入れていないと、初期化される。


## クラスを継承してみる
```
class Parent {
  constructor(fuga){
    this.fuga = fuga;
  }
  piyo() {
    alert(this.fuga);
  }
}

class Children extend Parent{
  constructor({ super();})
  //※子クラスではconstructor内でsuperを呼び出さないとエラーが出ます。

  piyo() {
    alert(this.fuga);
  }
  //↑同じメソッド名なので親クラスを上書き
}
//小クラスのコンストラクタの中で親クラスのコンストラクタを呼び出すにはsuper(...)を使用。
var child = new Child('child');
child.piyo();
```
※継承時には親クラスのconstructorが自動的に呼ばれます。
- 子クラスで親クラスと同名のメソッドを定義すればメソッドがオーバーライドされる。
- 子クラスから親クラスの【public】なメソッドを呼び出すにはsuper.methodName(...)のようにする。

■参考文献
-　-　-　-　-　-　-　-　-　-　-
http://tdak.hateblo.jp/entry/20140406/1396773476  
http://www.yunabe.jp/docs/javascript_class_es6.html    
http://qiita.com/M-ISO/items/bed1be20c8eaab1c7762  


## 継承を使う上での注意点
- 使いすぎると複雑になる
- 親クラスが子クラスに影響を与えるため修正があっても、時間がかかる or 出来ない


## どういうときにclassを使うべきか
- 変数がやたらめったら多い。  
→classを使えば、どこに所属している変数かが一目瞭然なので、迷子の変数がなくなる。
