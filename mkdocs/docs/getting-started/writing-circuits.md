---
description: >-
  This tutorial guide you through the process of writing your first program
  using the main features of circom: signals, variables, templates, components,
  and arrays.
---

# 回路の記述
`circom`では、プログラマーが演算回路を定義する[制約](../../circom-language/constraint-generation)を定義することができます。すべての制約は、A、B、Cがシグナルの線形結合である場合、A*B + C = 0の形でなければなりません。これらの式の詳細については、[こちら](../../circom-language/constraint-generation)を参照してください。

`circom`を使って作る演算回路は、シグナルを演算します。2つの入力シグナルを単純に乗算し、出力シグナルを生成する最初の回路を定義してみましょう。

```text
pragma circom 2.0.0;
  
/* この回路テンプレートは、cがaとbの掛け算であることを確認するものです。 */  

template Multiplier2 () {  

   // シグナルの宣言  
   signal input a;  
   signal input b;  
   signal output c;  
     
   // 制約
   c <== a * b;  
}
```
まず、`pragma`命令でコンパイラのバージョンを指定します。これは、`pragma`命令の後に示されるコンパイラのバージョンに対応した回路であることを確認するためのものです。そうでなければ、コンパイラが警告を投げます。

そして、予約キーワード`template`を使って、`Multiplier2`という新しい回路の形状を定義します。さて、その[シグナル](../../circom-language/signals)を定義しなければなりません。シグナルには`a`,`b`,`c`のような識別子を付けることができます。この回路では、2つの入力シグナル`a`,`b`と出力シグナル`c`があります。最後に`<==`を使って、`c`の値が`a`と`b`の値を掛け合わせた結果であることを設定しています。同様に、演算子`==>`を使って例えば`a * b ==> c`のようにできます。

各テンプレートでは、最初にシグナルを宣言し、その後に関連する制約を宣言していることに注目しましょう。

