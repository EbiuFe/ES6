------------------------------------------------------
# classの拡張について
------------------------------------------------------

## classとは？の前に、オブジェクトとは？
- データと処理の集まり・ひとつのテーマを持った集まり  
例）Userクラス
プロパティ（状態）→名前、住所、電話番号...  
メソッド（classに対する振る舞い・命令）→名前を返す、住所を振り分ける...

## classとは↑オブジェクトの設計書
- 家の設計図→class
- 実際に建てた家→インスタンス  
※設計図はドアは開けられないけど、インスタンスは実際に建っているのでドアが開けられる  
→つまり、インスタンス化（実体化）させないとclass作っても使えないよ！ということ。  

ただし例外もあります。＝静的メソッドと呼ばれるもの。  
これはclassに直接アクセスして使用できる仕様にもできる。（ふふ...  
ちなみにtypescriptならpublic/private/protectedを使用できます。（es6では検討中らしい？）  



## 継承について
何回も似たようなclassを作るのは非効率的。→継承させよう！

```  
class Hoge {
  constructor(fuga){
    this.fuga = fuga;
  }
  piyo() {
    alert(this.fuga);
  }
}
var hogetan = new Hoge("FUGA");
hogetan.piyo();
```  
#### constructorとは？
インスタンスが作られたときに自動的に最初に実行されるメソッド。  
constructorに何も値を入れていないと、初期化される。


■上のHogeクラスを継承してみる
```  
class Hoge {
  constructor(fuga){
    this.fuga = fuga;
  }
  piyo() {
    alert(this.fuga);
  }
}

class HogeSub extend Hoge{
  constructor({ super();})
  //※子クラスではconstructor内でsuperを呼び出さないとエラーが出ます。
}
```  


■参考文献  
-　-　-　-　-　-　-　-　-　-　-  
http://tdak.hateblo.jp/entry/20140406/1396773476
