------------------------------------------------------
# classの拡張について
------------------------------------------------------

## classとは？の前に、オブジェクトとは？
- データと処理の集まり・ひとつのテーマを持った集まり  
例）Userクラス
プロパティ（状態）→名前、住所、電話番号...  
メソッド（classに対する振る舞い・命令）→名前を返す、住所を振り分ける...   
→**『データを元にクラスからインスタンスを生成し、インスタンスはそのデータを元にして仕事をする』**  
というのがオブジェクト指向の基本形の考え方

## classとは↑オブジェクトの設計書
- 家の設計図→class
- 実際に建てた家→インスタンス  
※設計図はドアは開けられないけど、インスタンスは実際に建っているのでドアが開けられる  
→つまり、インスタンス化（実体化）させないとclass作っても使えないよ！ということ。  

ただし例外もあります。＝静的メソッドと呼ばれるもの。  
これはclassに直接アクセスして使用できる仕様にもできる。（ふふ...  
ちなみにtypescriptならpublic/private/protectedを使用できます。（es6では検討中らしい？）  
private変数は「アンダースコア」を付けて定義するのがgoogle jsスタイルガイド流。    

> - public => どこからでもアクセス可能  
> - protected => クラス内、同一パッケージ、サブクラスからアクセス可  
> - private => クラス内のみアクセス可能
>
> つまり，privateは自分からしか見えないメソッドであるのに対して，protectedは一般の人からは見られたくないが，仲間(クラスが同じオブジェクト)からは見えるメソッドです．  
protectedは例えば2項演算子の実装にもう一方のオブジェクトの状態を知る必要があるか調べる必要があるが，そのメソッドをpublicにして，広く公開するのは避けたいというような時に使います．

■参考  
[google style guide 日本語訳](https://www38.atwiki.jp/aias-jsstyleguide2/pages/13.html)  
[google js styleguide](https://google.github.io/styleguide/javascriptguide.xml)  
[スタイルガイドや命名規則記事まとめ](http://qiita.com/kitsuki00/items/79428876391d0179a2ca)


## 継承について
----------------------------------------------------------
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
-


## どういうときにclassを使うべきか
- 変数がやたらめったら多い。
→classを使えば、どこに所属している変数かが一目瞭然なので、迷子の変数がなくなる。
