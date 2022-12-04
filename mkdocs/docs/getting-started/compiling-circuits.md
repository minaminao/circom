---
description: >-
  This tutorial guide you through the process of writing your first program
  using the main features of circom: signals, variables, templates, components,
  and arrays.
---
# Compiling our circuit
コンパイラをインストールすると、以下のように利用可能なオプションが表示されます。

```console
circom --help

   Circom Compiler 2.0
   IDEN3
   Compiler for the Circom programming language

   USAGE:
      circom [FLAGS] [OPTIONS] [input]

   FLAGS:
      -h, --help       Prints help information
         --inspect    Does an additional check over the constraints produced
         --O0         No simplification is applied
      -c, --c          Compiles the circuit to c
         --json       outputs the constraints in json format
         --r1cs       outputs the constraints in r1cs format
         --sym        outputs witness in sym format
         --wasm       Compiles the circuit to wasm
         --wat        Compiles the circuit to wat
         --O1         Only applies var to var and var to constant simplification
      -V, --version    Prints version information

   OPTIONS:
         --O2 <full_simplification>    Full constraint simplification [default: full]
      -o, --output <output>             Path to the directory where the output will be written [default: .]

   ARGS:
      <input>    Path to a circuit with a main component [default: ./circuit.circom]
```
[初めての回路の記述](../writing-circuits)で`Multiplier2`というテンプレートを作りました。
しかし、実際に回路を作成するには、このテンプレートのインスタンスを作成する必要があります。そのために、以下のような内容のファイルを作成します。

```text
pragma circom 2.0.0;

template Multiplier2() {
    signal input a;
    signal input b;
    signal output c;
    c <== a*b;
 }

 component main = Multiplier2();
```
`circom`を使って演算回路を書いたら、それを拡張子`.circom`のファイルに保存しておく必要があります。回路は自分で作成してもよいですし、回路ライブラリ[`circomlib`](https://github.com/iden3/circomlib)のテンプレートを使ってもよいことを忘れないでください。

この例では、*multiplier2.circom*というファイルを作成します。
あとは回路をコンパイルして、回路を表す算術方程式系を得ます。コンパイルの結果、ウィットネスを計算するプログラムも得られます。
コンパイルは次のコマンドで行えます。

```text
circom multiplier2.circom --r1cs --wasm --sym --c
```

これらのオプションで、3種類のファイルを生成します。

* `--r1cs`: 回路の[R1CS](../../background/background#rank-1-constraint-system)をバイナリ形式で含むファイル`multiplier2.r1cs`を生成します。

* `--wasm`: `Wasm`のコード（multiplier2.wasm）と[ウィットネス](../../background/background#witness)を生成するために必要なその他のファイルを含むディレクトリ`multiplier2_js`を生成します。

* `--sym`: 注釈付きモードでConstraint Systemのデバッグや出力に必要なシンボルファイル`multiplier2.sym`を生成します。

* `--c`: witnessを生成するためのCコードのコンパイルに必要ないくつかのファイル（multiplier2.cpp、multiplier2.dat、また、main.cppやMakeFileなどのコンパイルするプログラムに共通のファイル）を含むディレクトリ`multiplier2_cpp`が生成されます。

これらのファイルを作成するディレクトリを指定するために、オプション`-o`を使用できます。

バージョン2.0.8以降では、オプション`-l`を使用して、ディレクティブ`include`が示された回路を探すべきディレクトリを示すことができるようになりました。

