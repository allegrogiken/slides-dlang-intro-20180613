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
- mutable/immutable
- 型推論もあるよ

---

## D言語: dlang とは

- 組み込み機能が多い
  - 単体テスト
  - 契約プログラミング
  - マクロっぽいやつ

---

# コード例で紹介
https://run.dlang.io/

---

## 🍩 Hello World

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

---

## 🧀 配列(スライス)

https://run.dlang.io/is/8kRVWl
```D
auto array = [ 1, 3, 5, 7, 9 ];

writeln(array);
writeln(array[1]);     // 3
writeln(array[1..4]);  // [3, 5, 7]
writeln(array.length); // 5
```

---

## 🗺️ 連想配列
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

## 🔓 immutable
https://run.dlang.io/is/xhoF14
```D
immutable int a = 5;
a = 10; // Error

immutable(int)[] b = [1, 2, 3];
b = [2, 4, 6]; // Pass
b[1] = 5; // Error

immutable(int[]) c = [1, 2, 3];
c = [2, 4, 6]; // Error
c[1] = 5; // Error
```

---

## ✅ unittest
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