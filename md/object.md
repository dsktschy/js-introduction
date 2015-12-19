1\. オブジェクト



---
1-1. 値には基本型と参照型がある  
　  
1-2. オブジェクトとはプロパティの集合である  
　  
1-3. 配列もオブジェクトである  
　  
1-4. 関数もオブジェクトである  
　  
1-5. メソッドとは関数を値とするプロパティである  
　  
1-6. 数値, 文字列, 論理値もメソッドを持っている？



---
**1-1. 値には基本型と参照型がある**
___
JSの値は、基本型か参照型かのどちらかである。  
　  
基本型 ： 数値, 文字列, 論理値, null, undefined  

参照型 ： オブジェクト(基本型でない全ての値)  
　  
基本型でない値はすべて参照型＝オブジェクトである。  
配列も、関数も、オブジェクトである。  
　  
変数代入時や引数に渡す際、  
基本形は値渡し、参照型は参照渡しで扱われる。
```
var numA = 10;
var numB = numA;
numA += numB;
alert(numA);            // 20
alert(numB);            // 10

var objA = {p1: 10};
var objB = objA;
objA.p2 = 20;
alert(objA.p2);         // 20
alert(objB.p2);         // 20
```



---
**1-2. オブジェクトとはプロパティの集合である**
___
JSにおけるオブジェクトは、  
名前(キー)と値からなるプロパティの集合である。  

連想配列の性質を持つと言える。
```
var obj = {
  name: 'Taro',
  'age': 20,
  "favorite_color": ['blue', 'white']
};

alert(obj.name);       // Taro
alert(obj['name']);    // Taro

var key = 'age';
alert(obj[key]);       // 20
alert(obj.key);        // undefined
```
　  
予めの宣言(クラス)を必要とせず、  
自由にプロパティの追加・削除が可能。
```
var obj = {};

obj.name = 'Jiro';
alert(obj.name);      // Jiro

delete obj.name;
alert(obj.name);      // undefined
```



---
**1-3. 配列もオブジェクトである**
___
JSにおける配列とは、  
配列の性質を持った特殊なオブジェクトである。  
　  
非負の整数をキーとするプロパティの集合であり、  
  
要素数を値に持つlengthプロパティと、  
様々な配列操作メソッドを持つ。
```
var arr = ['Taro', 'Ichiro', {name: 'Jiro'}];

alert(arr[0]);         // Taro
alert(arr[1]);         // Ichiro
alert(arr[2].name);    // Jiro

alert(arr.length);     // 3

arr.push('Saburo');
alert(arr[3]);         // Saburo

alert(arr.length);     // 4

alert(arr['0']);       // Taro
// alert(arr.0);       // error
```



---
ただし、配列の性質を持つオブジェクトを自作しても、  
配列の特殊性は得られない。
```
var arrayLike = {
  0: 'a',
  1: 'b',
  length: 2
};

arrayLike['2'] = 'c';
alert(arrayLike.length);    // 2

arrayLike.push('d');        // error
```



---
**1-4. 関数もオブジェクトである**
___
JSにおける関数とは、  
関数の性質を持った特殊なオブジェクトである。  
　  
後ろに()を付けることで、  
引数をとり、呼び出すことができ、  
定義中のreturn文により値を返す。
```
var sayTaro = function() {
  alert('Taro');
};
sayTaro();                        // Taro

var sayName = function(name) {
  alert(name);
};
sayName('Jiro');                  // Jiro

var getSaburo = function() {
  return 'Saburo';
};
alert(getSaburo());               // Saburo
```



---
**1-5. メソッドとは関数を値とするプロパティである**
___
JSにおけるメソッドとは、  
関数を値とするプロパティである。
```
var obj = {
  name: 'Saburo',
  sayName: function() {
    alert(this.name);
  }
};

obj.sayName();                // Saburo

var funcName = 'sayName';
obj[funcName]();              // Saburo

obj.askName = function() {
  alert("What's your name?");
};
obj.askName();                // What's your name?

delete obj.askName;
obj.askName();                // error
```



---
**1-6. 数値, 文字列, 論理値もメソッドを持っている？**
___
数値, 文字列, 論理値は、オブジェクトでないにも関わらず、  
自身からメソッドを呼び出すことができる。
```
var num = 0.0001;
alert(num.toExponential());    // 1e-4

alert('Taro'.charAt(2));       // r

alert(true.toString());        // true
```
　  
数値, 文字列, 論理値はそれぞれに  
**ラッパーオブジェクト Number, String, Boolean** を持ち、  
  
ラッパーオブジェクトのプロパティ、メソッドは  
自身のプロパティ、メソッドかの様に扱うことができる。  
　  
ラッパーオブジェクトは、メソッド呼び出し時に  
内部で自動的に生成・破棄されている。
```
alert('Taro'.charAt(2));       // r

// var tempString = new String('Taro');
// var tempResult = tempString.charAt(2);
// tempString = null;
// alert(tempResult);
```