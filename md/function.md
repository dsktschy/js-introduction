2\. 関数



---
2-1. 関数の宣言・定義  
　  
2-2. 宣言は巻き上げられる  
　  
2-3. ローカルスコープは関数ブロックにのみ存在する  
　  
2-4. クロージャがよく使われる(のでその説明)



---
**2-1. 関数の宣言・定義**
___
他言語と同じように宣言もできるが、
```
function func(message) {
  alert(message);
}
func('Hello!');
```
　  
JSにおける関数はオブジェクトであるため、  
変数に代入することができる。
```
var func = function(message) {
  alert(message);
};
func('Hello!');
```
　  
また、変数に代入せず、  
()で囲った定義からそのまま呼び出すこともできる。  

→ **即時関数**
```
(function(message) {
  alert(message);
})('Hello!');
```



---
**2-2. 宣言は巻き上げられる**
___
JSでは、変数・関数の宣言が  
スコープの開始部分まで巻き上げられる。  

ただし、定義は元の位置にあるものとして扱われる。  
```
// alert(a);    // error

alert(b);       // undefined
var b;
b = 1;
alert(b);       // 1
```
　  
関数も、変数に代入する場合は同様に扱われるが、
```
alert(func);                      // undefined
func('Hello!');                   // error
var func = function(message) {
  alert(message);
};
```
　  
変数に代入しない場合、宣言が定義ごと巻き上げられる。
```
alert(func);                // function func(...
func('Hello!');             // Hello!
function func(message) {
  alert(message);
}
```



---
**2-3. ローカルスコープは関数ブロックにのみ存在する**
___
JSでは、関数以外のブロックにスコープが存在しない。
```
if (true) {
  var str = 'str';
}
alert(str);    // str
```
　  
関数ブロックにのみスコープが存在する。
```
var func = function () {
  var str = 'str';
};
alert(str);    // error
```



---
**2-4. クロージャがよく使われる(のでその説明)**
___
JSでは、スコープのローカル変数は  
スコープ開始の度に新たに作られ、  
スコープ終了の度に消去される。  

→ **ガベージコレクション**
```
var func = function() {
  var num = 0;
  alert(++num);
};

func();    // 1
func();    // 1
```



---
ただし、スコープ終了後も参照されるローカル変数は、  
ガベージコレクションの対象とならない。
```
var func = (function() {
  var num = 0;
  return function() {
    alert(++num);
  };
})();

func();    // 1
func();    // 2
```
　  
変数 func に代入された関数オブジェクトの様に、  

関数と、その関数が定義されたスコープとが  
一体となった関数オブジェクトを **クロージャ** と呼ぶ。



---
クロージャの実用例1：private属性の模倣
```
var counter = (function() {

  var privateVal = 0;          // private変数の模倣

  var add = function(val) {    // privateメソッドの模倣
    privateVal += val;
  };

  return {
    increment: function() {
      add(1);
    },
    value: function() {
      return privateVal;
    }
  };

})();

alert(counter.privateVal);    // undefined
alert(counter.value());       // 0
counter.increment();
alert(counter.value());       // 1
counter.add(1);               // error
```
変数privateVal, addには、  
counterオブジェクトのメソッドからのみアクセス可能であり、  
直接はアクセスできない。  
  
変数privateVal, addは、counterオブジェクトの  
private属性のような働きをする。



---
クロージャの実用例2：非同期処理におけるthisの保存

```
var person = {

  name: 'Taro',

  who: function() {
    alert(this.name);    // this -> person
  }

};

person.who();     // Taro
```
thisは、オブジェクト内ではオブジェクト自身を指すが、
```
var person = {

  name: 'Taro',

  who: function() {
    $.ajax({
      success: function() {
        alert(this.name);      // this -> $.ajaxの引数
      }
    });
  },

};

person.who();           // undefined
```
コールバック関数内では、オブジェクト内であっても  
別のオブジェクトを指す。  



---
コールバック関数内からオブジェクト自身を参照するには、   
コールバック関数外でthisを変数に代入し、  
コールバック関数をクロージャにすればよい。
```
var person = {

  name: 'Taro',

  who: function() {
    var _this = this;             // this -> person
    $.ajax({
      success: function() {
        alert(_this.name);        // _this -> person (this -> $.ajaxの引数)
      }
    });
  }

};

person.who();    // Taro
```