3\. プロトタイプ



---
3-1. オブジェクトはオブジェクトを元に作られる  
　  
3-2. インスタンスはプロトタイプのプロパティを継承する  
　  
3-3. 組み込みのプロトタイプが存在する  
　  
3-4. 全てのオブジェクトはプロトタイプを持つ  
　  
3-5. プロトタイプについてより深く理解するならコチラ



---
**3-1. オブジェクトはオブジェクトを元に作られる**
___
JSのオブジェクトは、クラスではなく、  
オブジェクトを元に生成される。  
  
生成されたオブジェクトを **インスタンス** 、  
元となったオブジェクトを **プロトタイプ** と呼ぶ。  
　  
クラスを元にオブジェクトを生成する言語が  
クラスベースのオブジェクト指向言語と呼ばれるのに対し、  

プロトタイプを元にオブジェクトを生成する言語は  
プロトタイプベースのオブジェクト指向言語と呼ばれる。  
　  
JSは、プロトタイプベースのオブジェクト指向言語である。



---
プロトタイプを元にオブジェクトを生成するには、  
主に次のどちらかが使用される。  
　  
　  
**Object.create()**  
　  
引数に渡したオブジェクトを  
プロトタイプとするオブジェクトを生成する。  
  
プロトタイプベースでの生成をシンプルに表現できるが、  
ECMAScript5未満の環境では使用できない。
```
var parent = {};

var child = Object.create(parent);
```



---
**new Constructor()**  
　  
関数オブジェクトをnew演算子に続けて呼び出すことで、  
関数オブジェクトのprototypeプロパティに格納した  
オブジェクトをプロトタイプとするオブジェクトを生成する。  
  
オブジェクト生成に使われる場合の関数オブジェクトを  
**コンストラクタ** と呼び、関数の内容は  
オブジェクト生成時に実行される。  

ECMAScript5未満の環境で使用でき、  
クラスベースに似た書き方ができるため、  
Object.create()に比べこちらの方が多く使われている。
```
var parent = {};

var Child = function() {};
Child.prototype = parent;
var child = new Child();
```



---
**3-2. インスタンスはプロトタイプのプロパティを継承する**
___
インスタンスは、プロトタイプのプロパティを参照(継承)し、  
自身のプロパティと同様に扱うことができる。  
　  
参照の関係にあるため、プロトタイプのプロパティの状態は  
常にインスタンスに共有される。
```
var parent = {
  species: 'human',
  saySpecies: function() {
    alert('I am a ' + this.species + '.');
  }
};

var child1 = Object.create(parent);
alert(child1.species);                 // human
child1.saySpecies();                   // I am a human.

var Child = function() {};
Child.prototype = parent;
var child2 = new Child();
alert(child2.species);                 // human
child2.saySpecies();                   // I am a human.

parent.species = 'dog';
alert(child1.species);                 // dog
alert(child2.species);                 // dog
```



---
プロパティにインスタンス間で異なる値を持つには、  
プロトタイプではなく、一つ一つのインスタンスに  
同名のプロパティを持たせることで実現する。  
　  
Object.create()で生成する場合は、  
生成されたインスタンスに直接プロパティを追加する。  
　  
new演算子で生成する場合は、コンストラクタ内で  
thisを介してインスタンスにプロパティを追加する。



---
下記の例では、Object.create()とプロパティ追加を  
関数にまとめることで、コンストラクタと同様に扱っている。
```
var parent = {
  sayName: function() {
    alert('I am ' + this.name + '.');
  }
};

var createChild = function(_name) {
  var child = Object.create(parent);
  child.name = _name;
  return child;
};
var child1 = createChild('Taro');
var child2 = createChild('Jiro');
alert(child1.name);                  // Taro
alert(child2.name);                  // Jiro
child1.sayName();                    // I am Taro.
child2.sayName();                    // I am Jiro.

var Child = function(_name) {
  this.name = _name;
};
Child.prototype = parent;
var child3 = new Child('Saburo');
var child4 = new Child('Shiro');
alert(child3.name);                  // Saburo
alert(child4.name);                  // Shiro
child3.sayName();                    // I am Saburo.
child4.sayName();                    // I am Shiro.
```



---
**3-3. 組み込みのプロトタイプが存在する**
___
JSには、予めいくつかのプロトタイプが用意されている。  

同時に、そのインスタンスを生成するコンストラクタも  
用意されており、プロトタイプは  
コンストラクタのprototypeプロパティに予め設定されている。  
　  
**Object.prototype**  
  
{}リテラルにより生成されるオブジェクト  
のプロトタイプ  
　  
**Function.prototype**  
  
function文により生成されるオブジェクト(関数)  
のプロトタイプ  
　  
**Array.prototype**  
  
[]リテラルにより生成されるオブジェクト(配列)  
のプロトタイプ



---
**RegExp.prototype**  
  
//リテラルにより生成されるオブジェクト(正規表現)  
のプロトタイプ  
　  
**Number / String / Boolean.prototype**
  
数値、文字列、論理値それぞれのラッパーオブジェクト  
のプロトタイプ  
　  
**Date.prototype**  
  
new Date()により生成される時間を表すオブジェクト  
のプロトタイプ  
　  
**Error.prototype**  
  
new Error()により生成されるエラーを表すオブジェクト  
のプロトタイプ



---
組み込みプロトタイプから生成されるインスタンス間で  
共用できるメソッドは、  
全てプロトタイプが持つメソッドを継承したものである。
```
var arr = ['Taro', 'Jiro', 'Saburo'];
arr.push('Shiro');
alert(arr.hasOwnProperty('push'));                   // false
alert(Array.prototype.hasOwnProperty('push'));       // true

var func = function() {
  alert('Taro');
};
var newFunc = func.bind(this);
alert(func.hasOwnProperty('bind'));                  // false
alert(Function.prototype.hasOwnProperty('bind'));    // true

var str = 'Taro';
var thirdChar = str.charAt(2);
alert(str.hasOwnProperty('charAt'));                 // false
alert(String.prototype.hasOwnProperty('charAt'));    // true
```
　  
※hasOwnPropertyメソッドは、全てのオブジェクトから  
利用でき、引数に渡された文字列をキーとするプロパティが、  
呼び出したオブジェクト自身に存在するかどうかを判定する。



---
**3-4. 全てのオブジェクトはプロトタイプを持つ**
___
全てのオブジェクトはプロトタイプを持つ。  
プロトタイプ自身もオブジェクトであるためプロトタイプを  
持ち、そのプロトタイプもまたプロトタイプを持つ。  
  
このプロトタイプによるオブジェクト同士の連なりを  
**プロトタイプチェーン** と呼ぶ。  
　  
プロトタイプチェーンを遡っていくと、  
全てのチェーンはいずれObject.prototypeに辿り着く。  
  
Object.prototypeは特別で、プロトタイプとしてnullを持ち、
全てのプロトタイプチェーンの終端に位置している。



---
![PrototypeChain1](img/pc1.png)



---
プロトタイプチェーンは、プロパティの要求時に用いられる。  
　  
あるオブジェクトのプロパティが要求されると、  
JSエンジンはまず、オブジェクト自身が  
プロパティを持っていないか探す。  
  
もし存在しなかった場合、次はオブジェクトの  
プロトタイプがプロパティを持っていないか探す。  
　  
これを繰り返して、見つかれば値を返して次の処理へ移る。  
  
プロトタイプがnullになるまで見つからない場合、  
言い換えると、Object.prototypeにも存在しない場合は、  
undefinedを返して次の処理へ移る。



---
![PrototypeChain1](img/pc2.png)



---
また、見つかった段階で、更に祖先のプロトタイプが  
同名プロパティを持っていた場合、祖先のプロパティは  
無視される。→ オーバーライド
```
var grandParent = {
  species: 'homo sapiens'
};

var parent = Object.create(grandParent);
parent.species = 'human';                   // override

var child = Object.create(parent);

alert(child.species);                       // human
alert(parent.species);                      // human
alert(grandParent.species);                 // homo sapiens
```



---
**3-5. プロトタイプについてより深く理解するならコチラ**
___  
社外の他人の記事ですが、  
ここから更に深くプロトタイプを理解したいときは、  
ぜひ[コチラ](http://maeharin.hatenablog.com/entry/20130215/javascript_prototype_chain)を読んでみて下さい。  
個人的には他の何よりわかりやすい解説でした。  
　  
「プロトタイプ」の指すものが4つもあること  
  
プロトタイプ (用語としての)  
prototype プロパティ  
[[prototype]] プロパティ  
\_\_proto\_\_ プロパティ  
  
に、私は非常に混乱したのですが、  
この記事のおかげで上手く整理することが出来ました。  
　  
4つの「プロパティ」と、オマケで2つの「コンストラクタ」の
指すものを、以降にまとめました。  
  
記事を読んだ後で振り返る時に使えるといいな。。  




---
**プロトタイプ**  
　  
全てのオブジェクト※がそれぞれに一つ参照している、  
自身とは別のオブジェクトを指す用語。  
  
オブジェクトは、プロトタイプのプロパティを参照し、  
自身のプロパティと同様に扱える。  

また、参照する側のオブジェクトを、  
プロトタイプのインスタンスと呼ぶ。  
　  
　  
**prototype プロパティ**  
　  
全てのFunctionオブジェクトが持つプロパティ。  

値は、自身をコンストラクタとして生成される  
オブジェクトのプロトタイプ。  



---
**[[prototype]] プロパティ**  
　  
全てのオブジェクト※が持つ内部プロパティ。  
値は、自身のプロトタイプ。  

内部プロパティであるため、直接参照することはできない。  
　  
　  
**\_\_proto\_\_ プロパティ**  
　  
全てのオブジェクト※が持つプロパティ。  
値は、[[prototype]]プロパティ。  

このプロパティが実装されていない環境もあり、  
その場合はObject.getPrototypeOf()で代用可能。  
　  
　  
※Object.prototypeと、Object.create(null)の返り値を除く。  
これらの[[prototype]]にはnullが格納されている。



---
**コンストラクタ**  
　  
自身のprototypeプロパティのインスタンス生成を目的に  
(new演算子と共に)使用される場合の  
Functionオブジェクトを指す用語。  
　  
　  
**constructor プロパティ**  
　  
全てのオブジェクトが持つプロパティ。  
値は、自身のコンストラクタにあたるFunctionオブジェクト。