<div align="center">
<img src="circom-logo-black.png" width="460px" align="center"/>
</div>

[![Chat on Telegram](https://img.shields.io/badge/@iden3-2CA5E0.svg?style=flat-square&logo=telegram&label=Telegram)](https://t.me/iden3io)
[![Website](https://img.shields.io/website?up_color=blue&up_message=circom&url=https%3A%2F%2Fiden3.io%2Fcircom)](https://iden3.io/circom)
[![GitHub repo](https://img.shields.io/github/last-commit/iden3/circom?color=blue)](https://github.com/iden3/circom)
![Issues](https://img.shields.io/github/issues-raw/iden3/circom?color=blue)
![GitHub top language](https://img.shields.io/github/languages/top/iden3/circom)
![Contributors](https://img.shields.io/github/contributors-anon/iden3/circom?color=blue)

---

# <div align="center"><b>[Start here](https://iden3.github.io/circom/getting-started/installation/)</b></div>

---

# &#9888; Important deprecation note

現在の`circom`はRustで書かれたコンパイラです。JavaScriptで書かれた旧`circom`コンパイラは将来的に凍結されますが、[以前のcircomリポジトリ](https://github.com/iden3/circom_old)からダウンロードすることが可能です。

---

# About the circom ecosystem

circomコンパイラとそのエコシステムにより、回路の作成、テスト、ゼロ知識証明の作成が可能です。

## circom

`circom`は、`circom`言語で書かれた回路をコンパイルするためのRustで書かれたコンパイラです。
このコンパイラは、回路の表現を制約として出力し、異なるZK証明を計算するために必要なすべてを出力します。

## circomlib

`circom`では、`templates`と呼ばれる小さな汎用回路を組み合わせることで、大規模な回路を作成することが可能です。`circomlib`は`circom`のテンプレートをライブラリ化したもので、comparator、ハッシュ関数、デジタル署名、2進・10進変換器など、数百種類の回路を収録しています。独自のテンプレートを作成することもできますが、コーディングを始める前に、すでに作成されているテンプレートをご覧になることをお勧めします。

このパッケージには、`circomlib`で利用可能な回路のテストが既に含まれています。
また、このパッケージは、依存関係として npm パッケージ `circomlibjs`、`circom_tester`、`ffjavascript` をインストールします。

## circomlibjs

`circomlibjs`は、`circomlib`のいくつかの回路のwitnessを計算するプログラムを提供するJavascriptライブラリです。
このライブラリは、`circom`で生成されたwasmやcコードを使って`circomlib`の多くの回路から計算されたwitnessが
`circom`で生成されたwasmまたはcコードを使って`circomlib`で生成された多くの回路が、`circomlibjs`の対応するJavaScriptプログラムで生成されたものと一致するかどうかを確認するために使用されます。

パッケージには、これらのプログラムがsrcディレクトリに含まれています。
testディレクトリには、それ自身のテストが含まれています。toolsディレクトリには、いくつかの必要なパラメータを事前に計算するプログラムが含まれています。

## circom_tester

`circomtester`は、`circom`の回路をテストするためのツールを提供するnpmパッケージです。

## ffjavascript

`ffjavascript`は、Javascriptで有限体演算を行うためのコードをnpmでパッケージ化したものです。

## snarkjs

`snarkjs` は、`circom` で生成されたアーティファクトからZK証明を生成・検証するコードを含むnpmパッケージです。

# Visual summary <a id="visual-summary"></a>

![](https://gblobscdn.gitbook.com/assets%2F-MDt-cjMfCLyy351MraT%2F-ME35kSLplV3Z39JJsLE%2F-ME37Q2MlDc67k0-jzQS%2Fcircomsnarkjs.png?alt=media&token=4b1b1c11-a1d4-4048-8c3a-0c7b02f4930a)

