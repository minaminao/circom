---
description: >-
  Here, we provide some context on arithmetic circuits, and the rationale for
  the creation of a circuit compiler for zero-knowledge proofs.
---
# Background
## Zero-knowledge proofs <a id="zero-knowledge-proofs"></a>
最近、**zero-knowledge proofs** \（ZKPs）と呼ばれる一連の暗号プリミティブが、パブリックブロックチェーンや分散型台帳の世界を撹拌している。ZKPsは**solution to privacy**の問題として最初に登場したが、最近では完全な**solution to scalability**の問題としても立ち上がっている。その結果、これらの暗号証明はブロックチェーン・コミュニティにとって非常に魅力的なツールとなり、最も効率的なアルゴリズムがすでにいくつかのアプリケーションに導入され統合されています。

ゼロ知識証明とは、**prover**と呼ばれる一方の当事者に、ある文が真であることを、その文の真偽以上の情報を明かすことなく、**verifier**と呼ばれる他方の当事者に納得させることができるプロトコルである。例えば、証明者は以下のような文に対する証明を作成することができる。

* *"I know the private key that corresponds to this public key"* : この場合、証明は秘密鍵に関する情報を一切明らかにしない。

* *"I know a private key that corresponds to a public key from this list"* : 前回と同様に、証明は秘密鍵に関する情報を明らかにしないが、この場合、関連する公開鍵も非公開のままである。

* *"I know the preimage of this hash value"* : この場合、証明は証明者が前像を知っていることを示すが、その前像の値に関する情報は一切明かさない。

* *"This is the hash of a blockchain block that does not produce negative balances"* : この場合、証明はブロックに含まれる取引の金額、起源、目的地に関するいかなる情報も明らかにしない。

**Non-interactive zero-knowledge proofs** \ (NIZK) は、ゼロ知識証明の特殊なタイプで、証明者が検証者と対話せずに証明を生成することができます。NIZKプロトコルは、**allow a smart contract to act as a verifier**.Ethereumのブロックチェーンアプリケーションに非常に適しています。この方法では、誰でも証明を生成し、それを取引の一部としてスマートコントラクトに送信することができ、スマートコントラクトは証明が有効かどうかに応じて何らかのアクションを実行することができます。

その中で、最も好ましいNIZKは、**succinct proof size**と**sublinear verification time**を持つ非対話的ゼロ知識プロトコルの集合である**zk-SNARK**証明 \(Zero-knowledge Succinct Non Interactive ARgument of Knowledgeこれらのプロトコルの重要性は2つあり、1つはプライバシー保証の向上に役立つこと、もう1つはその証明サイズの小ささがスケーラビリティの解決に利用されてきたことです。

## Arithmetic circuits <a id="arithmetic-circuits"></a>
zk-SNARKsは、多くのZKPと同様に**computational statements**を証明することができるが、計算問題に直接適用することはできず、まず計算文を正しい形に変換する必要がある。具体的には、zk-SNARKsは計算文を算術回路でモデル化する必要があります。この変換の方法は必ずしも明らかではないが、我々が注目するほとんどの計算問題は、容易に算術回路に変換することが可能である。

**`F_p`-arithmetic circuit**は、フィールド`F_p`からの値を伝送し、加算・乗算ゲート`modulo p`に接続する一連の配線で構成される回路である。

👉 素数`p`が与えられたとき、**finite field** **`F_p`**は、`p`のモジュロでこれらの数を足したりかけたりできる数`{0,...,p-1}`の集合で構成されていることを思い出してください。

例えば、有限体`F_7`は、`{0,...,6}`の数の集合からなり、`7`のモジュロで加算・乗算ができる。モジュロ`7`の演算を簡単に理解するには、時計の針が何回回ったかは気にせず、何時何分に針が回るかのみを気にする**think of a clock of 7 hours**を考えればよい。つまり、7で割ったときの注意点だけを気にするのである。

* `15 modulo 7 = 1`、`15 = 7 + 7 + 1`から

* `7 modulo 7 = 0`

* `4*3 modulo 7 = 5`、`4*3 = 12 = 7 + 5`から

## Signals of a circuit <a id="signals-of-a-circuit"></a>
そこで、演算回路は`0,...,p-1`の間の値である**input signals**をいくつか取り、それらの間で素数`p`のモジュロで加算と乗算を実行する。すべての加算・乗算ゲートの出力は、回路の最後のゲートを除いて、**intermediate signal**とみなされ、その出力**,**が回路の**output signal**となる。

**Ethereum**でzk-SNARK証明を生成・検証するためには、素数をとって`F_p`の算術回路を扱う必要がある。

```text
p = 21888242871839275222246405745257275088548364400416034343698204186575808495617
```
下図では、演算を行う`F_7`-算術回路を定義しています。`out = a*b + c`.この回路は5つの信号を持ち、信号`a`、`b`、`c`は入力信号、`d`は中間信号、`out`は回路の出力である。

![](https://gblobscdn.gitbook.com/assets%2F-MDt-cjMfCLyy351MraT%2F-MHR5icu-Jxuas-UC7DY%2F-MHR60RuAQK6qNzhOPgE%2Foutput.jpg?alt=media&token=39d3d332-cac5-4546-ab43-9f489241ae50)

### ​ <a id="undefined"></a>
zk-SNARKプロトコルを使うには、信号間の関係を変数とゲートを関係づける方程式系として記述する必要があります。これ以降、回路を記述する方程式を**constraints**と呼び、その回路の信号が満たすべき条件と考えればよい。

## Rank-1 constraint system <a id="rank-1-constraint-system"></a>
信号`s_1,...,s_n`を持つ演算回路があるとすれば、**constraint**を次のような形の方程式で定義する。

`(a_1*s_1 + ... + a_n*s_n) * (b_1*s_1 + ... + b_n*s_n) + (c_1*s_1 + ... + c_n*s_n) = 0`

なお、制約**must be quadratic, linear or constant equations**は、小さな修正(変数の変更や2つの制約を集めるなど)を行うことで、制約や変数の数を減らすことができる場合があります。一般に、回路には複数の制約条件(通常、乗法ゲートごとに1つ)があります。回路を記述する制約の集合を**rank-1 constraint system** \(R1CS)と呼びます。

`(a_11*s_1 + ... + a_1n*s_n)*(b_11*s_1 + ... + b_1n*s_n) + (c_11*s_1 + ... + c_1n*s_n) = 0`

`(a_21*s_1 + ... + a_2n*s_n)*(b_21*s_1 + ... + b_2n*s_n) + (c_21*s_1 + ... + c_1n*s_n) = 0`

`(a_31*s_1 + ... + a_3n*s_n)*(b_31*s_1 + ... + b_3n*s_n) + (c_31*s_1 + ... + c_1n*s_n) = 0`

`...`

`...`

`(a_m1*s_1 + ... + a_mn*s_n)*(b_m1*s_1 + ... + b_mn*s_n) + (c_m1*s_1 + ... + c_mn*s_n) = 0`

回路内の演算はある素数`p`のモジュロで行われることを覚えておいてほしい。つまり、上の式はすべて`modulo p`で定義されている。

先の例では、この回路のR1CSは次の2つの式で構成されています。

* `d = a*b modulo 7`

* `out = d+c modulo 7`

この場合、変数`d`を直接置き換えることで、2つの方程式を1つにまとめることができる。

* `out = a*b + c modulo 7`

回路についての良いところは、多くの開発者にとって圧倒的であることができるほとんどの**zero-knowledge protocols have an inherent complexity**が、**design of arithmetic circuits is clear and neat**であるということです。

👉 `circom`では、自分で回路を設計し、自分で制約を設定し、コンパイラはゼロ知識証明に必要なR1CS表現を出力します。

ゼロ知識は、**circuit satisfiability**を証明することができる。これはどういうことかというと、回路を満たす信号の集合を知っていること、言い換えれば、R1CSの解を知っていることを証明できるのです。この信号の集合を**witness**と呼ぶ。

## Witness <a id="witness"></a>
入力のセットがあれば、中間信号と出力信号の計算は非常に簡単である。つまり、任意の入力の集合があれば、残りの信号は必ず計算できる。では、なぜ回路充足性の話をする必要があるのでしょうか？ゼロ知識証明の重要な点は、信号に関する情報を明らかにすることなく、これらの回路を計算することができることです。

例えば、先の回路で入力`a`が秘密鍵、入力`b`がそれに対応する公開鍵であったとする。`b`は公開しても構わないが、`a`は絶対に公開したくないと思うだろう。`a`を秘密入力、`b`、`c`を公開入力、`out`を公開出力と定義すると、ある公開値`b`、`c`、`out`に対して、式`a*b + c = out mod 7`が成り立つような秘密入力`a`を知っていることを、ゼロ知識でその値を明かすことなく証明することができる。

> `a`の値を他の信号から分離すれば、容易に推論できることに注意。プライベート入力のプライバシーを守り、R1CSから推論されないように回路設計することが重要である。

信号の割り当てを**witness**と呼ぶ。例えば、`{a = 2, b = 6, c = -1, out = 4}`はこの回路の有効な証人となる。`{a = 1, b = 2, c = 1, out = 0}`は`a*b - c = out`の式を満たさないので、有効な証人とはならない。

## Summary <a id="summary"></a>
**In summary, zk-SNARK proofs are an specific type of zero-knowledge proofs that allow you to prove that you know a set of signals \(witness\) that match all the constraints of a circuit without revealing any of the signals except the public inputs and the outputs.**

