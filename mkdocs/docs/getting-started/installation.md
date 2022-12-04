---
description: This tutorial will guide you through the installation of circom and snarkJS.
---

<!--
TODO add and mini explain ffjavascript
Put links to all the docs
-->

# circomエコシステムのインストール

## &#9888; 重要な推奨事項

JavaScriptで書かれた旧`circom`コンパイラは将来的に凍結されますが、[古いcircomリポジトリ](https://github.com/iden3/circom_old)からダウンロードすることは可能です。

## 依存関係のインストール

`circom`とその関連ツールを実行するために、システムにいくつかの依存関係が必要です。

* コアとなるツールは、Rustで書かれた`circom`コンパイラです。
Rustを利用できるようにするには、`rustup`をインストールします。LinuxやmacOSを使用している場合は、ターミナルを開いて以下のコマンドを入力します。

<!--
TODO remove the command and put a link to rustup site
-->

```shell
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

* また、一連のnpmパッケージを配布しているので、`Node.js`と`npm`や `yarn`などのパッケージマネージャがシステムで利用可能であるはずです。`Node.js`の最近のバージョンでは、大きな整数のサポートや、コードの実行を高速化するウェブアセンブリコンパイラが含まれているので、より良いパフォーマンスを得るには、バージョン10以上をインストールしてください。

## circomのインストール
私たちのソースからインストールするには、`circom`リポジトリをクローンしてください。

```text
git clone https://github.com/iden3/circom.git
```

circomディレクトリに入り、cargo buildでコンパイルします。

```text
cargo build --release
```

インストールが完了するまでには3分程度かかります。
コマンドが正常に終了すると、ディレクトリ`target/release`に`circom`のバイナリが生成されます。
このバイナリをインストールするには、次のようにします。

```text
cargo install --path circom
```

このコマンドは、`circom`のバイナリをディレクトリ`$HOME/.cargo/bin`にインストールします。

これで、`help`フラグを使えば、実行ファイルのすべてのオプションを見ることができるようになるはずです。

```console
circom --help

   Circom Compiler 2.0.0
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
## snarkjsのインストール <a id="installing-the-tools"></a>

`snarkjs` は、`circom` で生成されたアーティファクトからゼロ知識証明を生成・検証するコードを含むnpmパッケージです。

`snarkjs`は、以下のコマンドでインストールできます。

```text
npm install -g snarkjs
```
