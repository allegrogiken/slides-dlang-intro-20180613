## なんでも入ってる！D言語のご紹介

#### allegrogiken @ 2018-06-13 大正GeekNight

---

## スライドの趣旨

- 学習用に向けて D言語を紹介する
- 私のプログラマー人生に大きく影響したD言語に関心を持ってもらう

---

## D言語: dlang とは

- "Better C" を目指した言語
- C++/Go/関数型 を混ぜた感じ
- 強力なテンプレート(ジェネリクス)
- ガベージコレクション
- immutable
- 型推論
- 組み込み機能が多い
  - 単体テスト
  - 契約プログラミング
  - マクロ的なやつ
---

# コード例で紹介
https://run.dlang.io/

---

## ☀️ Hello World

https://run.dlang.io/is/hROZGN
```D
import std.stdio;

void main()
{
    // string hello = "Hello D World!";
    // auto hello = "Hello D World!";
    const hello = "Hello D World!";

    writeln(hello);
}
```
- 変数宣言の形はC系
- 型の代わりに `auto` で型推論
- 定数(const)も型推論

---

## 🧀 slices / 配列

https://run.dlang.io/is/8kRVWl
```D
auto array = [ 1, 3, 5, 7, 9 ];

writeln(array);
writeln(array[1]);     // 3
writeln(array[1..4]);  // [3, 5, 7]
writeln(array.length); // 5
```
- 1..4 みたいな `Range` 表現もできる
---

## 🗺️ associative array / 連想配列
https://run.dlang.io/is/ujCFas
```D
// int[string]
auto map = ["a" : 1, "b" : 2];

writeln(map);
writeln(map["a"]); // 1
writeln(map.length); // 2
writeln(map.keys); // ["b", "a"]
writeln(map.values); // [2, 1]
```

---

## ⚙️ function type
https://run.dlang.io/is/VXxjDl
```D
const int function(int) f1 = function(int x){ return x+1; };
const f2 = function(int x){ return x+1; }; // 型推論
const f3 = (int x){ return x+1; }; // `function` 省略形
const f4 = (int x) => x+1; // ラムダ式

writeln(f1(0)); // 1
writeln(f2(0)); // 1
writeln(f3(0)); // 1
writeln(f4(0)); // 1
```
- 関数が第一級オブジェクト
- lambda syntax も使える
---

## 🗿 OOP / オブジェクト指向
https://run.dlang.io/is/420oGB
```D
interface Animal{ void bark(); }

class Dog : Animal{
    void bark(){ writeln("waon!"); }
}
class Cat : Animal{
    void bark(){ writeln("meow-"); }
}

void main(){
    auto animals = cast(Animal[])[ new Dog(), new Cat() ];
    animals[0].bark(); // waon!
    animals[1].bark(); // meow-
}
```
- Javaに近いOOP（Classは単一継承、Interfaceは複実装可）
- 全てのClassは `Object` クラスのサブクラス
---

## 🔐️ immutable / 不変
https://run.dlang.io/is/eUouXZ
```D
    immutable int a = 5;
    a = 10; // Error
    const int b = 5;
    b = 10; // Error
    
    immutable(int)[] c = [1, 2, 3];
    c[1] = 5; // Error
    c = [2, 4, 6]; // Pass
    
    immutable int[] d = [1, 2, 3];
    d[1] = 5; // Error
    d = [2, 4, 6]; // Error
```
- 型のオプションとして `immutable` がある感じ
- `const` は スコープに働くimmutable
    - `const(int)[] x` はできない
- 定数としては `enum a = 5;` が向いている

---

## 👽 alias
https://run.dlang.io/is/HuWybU
```D
import std.stdio;

void main()
{
    alias im_int = immutable int;
    im_int a = 5;
    alias b = a;
    
    alias println = std.stdio.writeln;
    println(b); // 5
    b = 10; // Error
}
```
- 色々なものに別名を付けることができる
- 関数の型表現に使うとすっきり

---

## ✅ unittest / 単体テスト
https://run.dlang.io/is/OynfBn
```D
int addOne(int a) {
    return a+1;
}

unittest {
    assert(addOne(-1) == 0);
    assert(addOne(0) == 1);
    assert(addOne(1) == 1); // AssertError
}
```
- コンパイル時のオプションに -unittest を付ける
- 関数単位で、すぐ近くにテストコードを書く

---

## 🛡️ Contract / 契約プログラミング
https://run.dlang.io/is/omSAeG
```D
int divide(int a, int b)
in{ // 入力条件
    assert(b != 0);
}
do{
    return a / b;
}

writeln(divide(10, 2));
writeln(divide(10, 0)); // AssertError
```
- 関数の引数・結果に対して検証をかける
- チェック部分のコードが綺麗に分離できる
- `out(result){ ... }` で出力の検証もできる

---

## 🛡️ Contract / 契約プログラミング
https://run.dlang.io/is/KqIK6Y
```D
class Person{
    private string m_name;
    @property string name(string name){ return m_name = name; }
    this(string name){ m_name = name; }
    invariant{ // 不変条件
        assert(m_name!=null);
    }
}

auto person = new Person("Taro");
person.name = null; // AssertError
new Person(null); // AssertError
```
- クラスの状態（メンバ変数の値）に対して検証をかける
- メンバ関数の実行前後で検証

---

## 🎨 generics / ジェネリクス
https://run.dlang.io/is/VO9d5I
```D
class valuePrinter(T){
    T value;
    this(T value){ this.value = value; }
    void print(){ writeln("value: ", value); }
}

void main(){
    new valuePrinter!string("Taro").print(); // value: Taro
    new valuePrinter!int(5).print(); // value: 5
}
```
- 機能としては「テンプレート」をクラスに適用した例
- 型パラメータは `!` の後に渡す
- 複数の型パラメータも渡せる
    - `sample(T,S){ ... }`
    - `sample!(int, string)`

---

## 🎨 generics / ジェネリクス
https://run.dlang.io/is/jb0poZ
```D
T square(T)(T t){
  return t*t;
}

void main(){
	writeln(square!int(4)); // 16
	writeln(square(4)); // 16
	writeln(square(1.5)); // 2.25
    
    writeln(4.square()); // 16 UFCSとの組み合わせ
    writeln(1.5.square()); //2.25
    
	// writeln(square("A")); // Compile Error
}
```
- 関数にテンプレートを適用した例
- 型パラメータが推測できるので `!` もろとも省略できる
- 第一引数の型にメソッドが生えたように使える `UFCS` と組み合わせると強力
- `string` は * 演算子が使えないのでコンパイルエラー

---

## 🔯 mixin / ミクスイン
https://run.dlang.io/is/isxhl8
```D
template singleValuePrinter(T){
    T name;
    this(T name){ this.name = name; }
    void print(){ writeln("value: ", name); }
}
class stringValuePrinter{
    mixin singleValuePrinter!(string);
}
class intValuePrinter{
    mixin singleValuePrinter!(int);
}

void main(){
    new stringValuePrinter("Taro").print(); // value: Taro
    new intValuePrinter(5).print(); // value: 5
}
```
- PHPのtraitみたいなやつ（テンプレート実装を各所に展開）
- 型パラメータを与えつつ展開できる

---

## 🕸️ Web Framework (vibe.d)
http://code.dlang.org/packages/vibe-d
```D
import vibe.vibe;

void main()
{
	auto settings = new HTTPServerSettings;
	settings.port = 8080;
	settings.bindAddresses = ["::1", "127.0.0.1"];
	listenHTTP(settings, &hello);
	runApplication();
}

void hello(HTTPServerRequest req, HTTPServerResponse res)
{
	res.writeBody("Hello, World!");
}
```
- node/express に近そうな感じ

---

## やってみたくなった?
#### D言語基礎文法最速マスター
https://gist.github.com/repeatedly/2470712  
綺麗にまとまっているgist
#### DLang Tour
https://tour.dlang.org/tour/ja/welcome/welcome-to-d  
公式のハンズオン ブラウザのみで試せる
