---
description: >-

---

# ウィットネスの計算

## ウィットネスとは？

証明を作成する前に、回路のすべての制約に一致する回路のすべてのシグナルを計算する必要があります。そのために、このジョブをするのに役立つ`circom`によって生成された`Wasm`モジュールを使用します。また、`C++`のコードでも同様の方法で行うことができます（下記参照）。

まず、`Wasm`のコードから見ていきましょう。生成された`Wasm`バイナリと3つのJavaScriptファイルを使って、入力のファイルを提供するだけで、モジュールは回路を実行し、すべての中間シグナルと出力を計算します。入力、中間シグナル、出力の集合を[ウィットネス](../../background/background#witness)と呼びます。

今回は33という数値の因数分解ができることを証明したいです。`a = 3`と`b = 11`としましょう。

なお、入力の一方に1を、もう一方に33をとすることも可能です。したがって、この証明は、33という数値を因数分解できることを実際に示しているわけではありません。

標準的なjson形式で書かれた入力を含む`input.json`という名前のファイルを作成する必要があります。

JavaScriptは2<sup>53</sup>より大きな整数を正確に扱えないので、数値の代わりに文字列を使っています。

```text
{"a": "3", "b": "11"}
```

ここで、ウィットネスを計算し、それを含むバイナリファイル`witness.wtns`を`snarkjs`が受け入れるフォーマットで生成します。

`circom`コンパイラをフラグ`--wasm`と回路`multiplier2.circom`で呼び出すと、multiplier2.wasmの`Wasm`コードと必要な`JavaScript`ファイルすべてを含む`multiplier2_js`フォルダを見つけることができます。

## WebAssemblyによるウィットネスの計算 <a id="witness-from-wasm-directory"></a>
ディレクトリ`multiplier2_js`に入力し、ファイル`input.json`に入力を追加して実行します。

```text
node generate_witness.js multiplier2.wasm input.json witness.wtns
```

## C++によるウィットネスの計算 <a id="witness-from-c-directory"></a>

より高速な方法として、C++ディレクトリを使用して、以前のファイル`input.json`を使用してウィットネスを計算できます。このディレクトリは、`circom`コンパイラを`--c`のフラグ付きで使用するときに作成されます。この例では、コンパイラはウィットネスの計算に必要なすべての`C++`コードと、対応する実行プログラムを簡単に生成するためのMakefileを含む`multiplier2_cpp`フォルダーを作成します。

そのためには、ディレクトリ`multiplier2_cpp`に入り、実行します。

```text
make
```

先ほどのコマンドでは、`multiplier2`という実行ファイルが作成されます。

注意: C++のソースをコンパイルするために、あなたのシステムにインストールされている必要があるいくつかのライブラリに依存しています。
特に、`nlohmann-json3-dev`、`libgmp-dev`、`nasm`を使用しています。

実行ファイルが作成された後、入力ファイルとウィットネスファイルの名前を指定して実行します。

```text
./multiplier2 input.json witness.wtns
```

## ウィットネスファイル

この2つのプログラムは同じ`ẁitness.wtns`ファイルを生成する。このファイルは、実際の証明の作成に使用するツールである`snarkjs`と互換性のあるバイナリ形式でエンコードされています。

注意: 大きな回路でのウィットネスの計算はWASMよりC++の方が断然速いです。

<!--
g++ -pthread -o circuit-512-32-256-64 -I ../../Fr -I ../../ ../../main.cpp ../../Fr/fr.o ../../Fr/fr.cpp ../../calcwit.cpp ../../utils.cpp circuit-512-32-256-64.cpp -lgmp -O3

g++ -pthread -o circuit-512-32-256-64 -I Fr main.cpp Fr/fr.o Fr/fr.cpp calcwit.cpp utils.cpp circuit-512-32-256-64.cpp -lgmp -O3

g++ -pthread -o circuit-512-32-256-64 main.cpp fr.o fr.cpp calcwit.cpp utils.cpp circuit-512-32-256-64.cpp -lgmp -O3

前の行を実行するために、makeを使用することができます。
しかし、最初にいくつかの依存関係をインストールする必要があります。

sudo apt install libgmp3-dev nasm

./aliascheck_test 

Usage: ./aliascheck_test <input.json> <output.wtns>

wtnsは、ウィットネスのバイナリフォーマットです。

fr.asmは、アセンブリでのフィールド演算です。
fr_asm.oはそのファイルをnasmでコンパイルしたものです。

fr.cppは、そのプログラムを含むc++です。
-->