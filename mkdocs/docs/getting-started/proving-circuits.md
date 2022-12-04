# 回路の証明

回路をコンパイルし、適切な入力でウィットネスの計算を実行をすると、計算されたすべてのシグナルを含む.wtnsという拡張子のファイルと、回路を記述する制約を含む.r1csという拡張子のファイルが作成されます。両ファイルは証明の作成に使用されます。

では、`snarkjs`ツールを使って、入力に対する証明を生成し、検証してみましょう。特に、multiplier2を使って、**33の2つの因数を提供できることを証明します。** つまり、2つの整数`a`と`b`を掛け合わせると33という数値になるようなものを知っていることを示すのです。

[Groth16](https://eprint.iacr.org/2016/260) zk-SNARKプロトコルを使用します。
このプロトコルを使用するには、[Trusted Setup](../../background/background#trusted-setup)を生成する必要があります。
**Groth16は回路ごとにTrusted Setupが必要です**。より詳細には、Trusted Setupは2つの部分から構成されています。

- Powers of Tau。回路に依存しない。
- Phase 2。回路に依存する。

次に、Trusted Setupを作成するための非常に基本的なセレモニーと、[Groth16](https://eprint.iacr.org/2016/260)証明の作成・検証のための基本的なコマンドを提供します。関連する[背景](../../background/background)を確認し、[snarkjsチュートリアル](https://github.com/iden3/snarkjs)を確認することで、より詳細な情報を得ることができます。

## Powers of Tau <a id="my-first-trusted-setup"></a>
<!--
次のコマンドを入力することで、`snarkjs`の**help**にアクセスすることができます。

`$ snarkjs --help`

回路の一般的な**統計**を取得し、**制約**を印刷することができます。次のコマンドを実行するだけです。

```text
snarkjs info -c multiplier2.r1cs 
snarkjs print -r multiplier2.r1cs -s multiplier2.sym
```
-->

まず、新しい「Powers of Tau」のセレモニーを開始します。

```text
snarkjs powersoftau new bn128 12 pot12_0000.ptau -v
```

次に、セレモニーにコントリビューションします。

```text
snarkjs powersoftau contribute pot12_0000.ptau pot12_0001.ptau --name="First contribution" -v
```

これで、Powers of Tauへのコントリビューションがファイル*pot12_0001.ptau*に格納されたので、Phase 2に進むことができます。

## Phase 2 <a id="my-first-trusted-setup"></a>

**Phase 2**は**circuit-specific**です。
以下のコマンドを実行すると、このフェーズの生成が開始されます。

```text
snarkjs powersoftau prepare phase2 pot12_0001.ptau pot12_final.ptau -v
```

次に、Phase 2で使用する証明鍵と検証鍵を含む`.zkey`ファイルを生成します。
以下のコマンドを実行し、新しいzkeyを始めます。

```text
snarkjs groth16 setup multiplier2.r1cs pot12_final.ptau multiplier2_0000.zkey
```

Phase 2セレモニーにコントリビューションします。

```text
snarkjs zkey contribute multiplier2_0000.zkey multiplier2_0001.zkey --name="1st Contributor Name" -v
```

<!--
最新のzkeyを検証する
snarkjs zkey verify $1.r1cs pot12_final.ptau $1_0001.zkey

ランダムなビーコンを適用する。

```text
snarkjs zkey beacon multiplier2_0001.zkey multiplier2_final.zkey 0102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d1e1f 10 -n="Final Beacon phase2"
```
最終的なzkeyを検証する
snarkjs zkey verify $1.r1cs pot12_final.ptau $1_final.zkey

前回と同様に、エントロピー源となるランダムなテキストを入力するよう促されます。出力は`multiplier2_final.zkey`というファイルになり、これを**export the verification key**に使用することになる。

```text
snarkjs zkey export verificationkey multiplier2_final.zkey verification_key.json
```
ここで、`multiplier2_final.zkey`から検証キーをファイル`verification_key.json`にエクスポートします。

`.ptau`または`.zkey`ファイルの計算が正しいかどうか、常に**verify**することができます。

```text
snarkjs powersoftau verify pot12_final.ptausnarkjs zkey verify multiplier2.r1cs pot12_final.ptau multiplier2_final.zkey
```
すべてがチェックアウトされれば、出力のトップに以下のように表示されるはずです。

```text
[INFO]  snarkJS: Powers of Tau file OK![INFO]  snarkJS: ZKey OK!
```
また、コマンド`snarkjs zkey verify`は、`.zkey`ファイルが特定の回路に対応しているかどうかを確認する。
-->

検証鍵をエクスポートします。

```text
snarkjs zkey export verificationkey multiplier2_0001.zkey verification_key.json
```

## 証明の生成
ウィットネスが計算され、Trusted Setupが実行済みであれば、回路とウィットネスに関連する**zk-proofの生成**ができます。

```text
snarkjs groth16 prove multiplier2_0001.zkey witness.wtns proof.json public.json
```

このコマンドは、[Groth16](https://eprint.iacr.org/2016/260)プルーフを生成し、2つのファイルを出力します。

* `proof.json`: 証明を含みます。
* `public.json`: 公開された入出力の値を含みます。

## 証明の検証

**証明の検証**をするには、以下のコマンドを実行します。

```text
snarkjs groth16 verify verification_key.json public.json proof.json
```

このコマンドは、先ほどエクスポートした`verification_key.json`、`proof.json`、`public.json`のファイルを使って、証明が有効かどうかをチェックします。証明が有効な場合、コマンドは`OK`を出力します。

有効な証明は、回路を満たすシグナルのセットを知っていることを証明するだけでなく、使用する公開入出力が`public.json`ファイルに記述されているものと一致することを証明します。

## スマートコントラクトでの検証
👉 **Ethereumブロックチェーンで証明を検証**できる**Solidity検証器**を生成することも可能です。

まず、Solidityのコードをコマンドで生成する必要があります。

```text
snarkjs zkey export solidityverifier multiplier2_0001.zkey verifier.sol
```

このコマンドは検証鍵`multiplier2_0001.zkey`を受け取り、Solidityのコードを`verifier.sol`というファイルに出力します。このファイルからコードを取り出し、Remixにカット＆ペーストすることができます。このコードには2つのコントラクトが含まれていることがわかります。`Pairing`と`Verifier`です。`Verifier`コントラクトのみをデプロイする必要があります。

Rinkeby、Kovan、Ropstenのようなテストネットを最初に使用するとよいでしょう。JavaScript VMを使うこともできますが、ブラウザによっては検証に時間がかかり、ページがフリーズすることがあります。

`Verifier`には`verifyProof`という`view`関数があり、証明と入力が有効である場合にのみ`TRUE`を返します。呼び出しを容易にするために、`snarkJS`を使用して、入力によってコールのパラメータを生成できます。

```text
snarkjs generatecall
```

コマンドの出力をRemixの`verifyProof`メソッドのparametersフィールドにカット＆ペーストしてください。すべてがうまくいけば、このメソッドは`TRUE`を返すはずです。パラメータのほんの1ビットを変更してみると、検証可能な`FALSE`となることがわかります。

