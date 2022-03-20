# Qiskit Textbook Machine Learning翻訳
https://learn.qiskit.org/course/machine-learning/parameterized-quantum-circuits

```
# Parameterized quantum circuits
In this section, we introduce parameterized quantum circuits, then describe their properties and implement some examples used in quantum machine learning.
```
# パラメータ化された回路
この節ではパラメータ化された回路について紹介し、量子機械学習における性質や実装例を説明します。

```
## Introduction
Parameterized quantum circuits, where the gates are defined through tunable parameters, are a fundamental building block of near-term quantum machine learning algorithms. In the literature, depending on the context, parameterized quantum circuits are also called parameterized trial states, variational forms or ansatze.

Here is an example of a simple parameterized circuit, with two parameterized gates, a single-qubit -rotation gate, with a variable rotation , followed by a two-qubit controlled -rotation gate, with the same variable rotation :

We can create the parameterized circuit above using the QuantumCircuit and Parameter classes. The Parameter class lets us add rotation gates, without having to specify what the angles are yet:

If we want the gates to have different parameters, we can use two Parameters, or we create a ParameterVector, which acts like a list of Parameters:

As all quantum gates used in a quantum circuit are unitary, a parameterized circuit itself can be described as a unitary operation on  qubits,  , acting on some initial state , often set to . The resulting parameterized quantum state is  where  is a set of tunable parameters.
```
パラメータ化された回路はチューニング可能なパラメータにより量子ゲートが定義されたもので、"near-term(TODO:)"の量子機械学習アルゴリズムの基礎を担っています。

以下はシンプルなパラメータ化された回路の例で、2つのパラメータ化ゲート(回転角\thetaの1量子ビットz軸回転ゲート)の後に回転角\thetaの2量子ビット制御z軸回転ゲートが続いています。

このパラメータ化された回路は`QuantumCircuit`と`Parameter`クラスから生成することが出来ます。`Parameter`クラスは具体的な回転角を指定せずに回転ゲートを追加することが出来ます。

ゲートに異なるパラメータを渡したい場合は`Parameter`クラスを2つ使うこともできますし、`ParameterVector`という`Parameters`のリストのようなものも生成することも出来ます。

量子回路内の全ての量子ゲートはユニタリ性を持つので、パラメータ化された回路自体も初期状態TODO:(通常TODO:)に作用するn量子ビットのユニタリ演算子TODO:とあらわすことが出来ます。  
パラメータ回路を作用させた結果はTODO:となります。ここでTODO:はチューニング可能なパラメータです。

```
# Parameterized quantum circuit properties
How do we choose one parameterized circuit over the other? To use parameterized quantum circuits as a machine learning model, we need them to generalize well. This means that the circuit should be able to generate a significant subset of the states within the output Hilbert space
Hilbert space is what you get when you generalize the 3D space we're used to (called Euclidean space) to as many dimensions as you want. It’s named after David Hilbert. Read more.

. To avoid being easy to simulate on a classical computer, the circuit should also entangle qubits.

In Reference 1, the authors propose the measures of expressibility and entangling capability to discriminate between different parameterized quantum circuits. We can think of the expressibility of a circuit as the extent to which it can generate states within the Hilbert space, while the entangling capability of a circuit describes its ability to generate entangled states.
```
# パラメータ化された回路の性質
パラメータ回路をいくつかの選択肢から選ぶ場合、どのように選べばよいでしょうか？
量子機械学習でパラメータ化された回路を利用するためには、パラメータ化された回路についてきちんと一般化する必要があります。つまり回路はヒルベルト空間における部分集合の状態を生成できるべきであるということです。　　
また、古典コンピュータで容易にシミュレーションできないように回路は量子ビットをもつれさせる必要があります。

参考文献1において著者は別々のパラメータ化された回路を区別するために表現能力とエンタングリング容量という指標を提唱しています。回路の表現能力とはヒルベルト空間内に生成可能な状態の範囲と考えることができます。一方で回路のエンタングリング容量とはエンタングルされた状態を生成する能力といえます。

```
## Expressibility
The expressibility of a parameterized quantum circuit is essentially the coverage of the Hilbert space by the circuit's hypothesis space. Highly expressive parameterized quantum circuits can represent many different unitaries. There are many ways to represent this expressibility, one such example can be found in Reference 1. Here the authors quantify it by computing the extent to which the states generated from the circuit deviate from the uniform distribution
```
## 表現能力
パラメータ化された回路の表現能力とは本質的には回路の仮説空間によるヒルベルト空間の範囲です。TODO:
高い表現能力を持つパラメータ化された回路は多種多様なユニタリ演算子を表現することが出来ます。表現能力を表す方法はいくつかあり、参考文献1では一つの例が示されています。著者は回路から生成された量子状態の一様分布からの偏差を計算することで定量化しています。

```
Let's compare the two following single-qubit circuits to get a sense of what it means for a circuit to be expressible or not, by sampling 2000 output states for each circuit and plotting them on a Bloch sphere.

For Circuit A, we see output states distributed about the equator of the Bloch sphere. With Circuit B's additional parameterized -rotation gate, we can cover all the Bloch sphere with output states, but the coverage is not uniform; there are concentrations of points on the  and  poles of the Bloch sphere.

Expressibility is a way to quantify what we just observed for these single-qubit circuits. Circuit A would have a low expressibility score due to the limited set of states it can explore, whereas Circuit B would have a high expressibility score.
```
次の1量子ビット回路を比較してみて回路の表現能力が高いかどうかの勘所を抑えましょう。各回路の出力状態をサンプリングしてブロッホ球上にプロットします。

回路Aでは出力状態がブロッホ球の赤道上に分布しています。一方でパラメータ化された回転ゲートを加えた回路Bはブロッホ球の全域に分布しています。しかし分布は一様ではなくブロッホ球の+Xと-Xの極付近に集中しています。TODO:

表現能力は今1量子ビット回路で観察してきたことを定量化する方法です。回路Aはとり得る状態が限られているため表現能力は低く、一方で回路Bは表現能力が高いと言えます。

```
## Entangling capability
Entanglement is a key resource in quantum computing. The Meyer-Wallach measure is one of many metrics that measures how entangled a given state is. The Meyer-Wallach measure of an unentangled product state　is 0, while the Meyer-Wallach measure of a highly entangled state, such as the Bell state, is 1. In Reference 1, the authors define the entangling capability of a parameterized quantum circuit as the average Meyer-Wallach measure for the states it can generate.
```
## エンタングリング容量
エンタングルメントは量子コンピューティングにおいて重要です。量子状態のエンタングルの度合いを測る数多くの指標の一つとしてMeyer-Wallach測度というものがあります。Meyer-Wallach測度はエンタングルメントの無い状態では0、ベル状態など十分エンタングルされた状態では1をとります。Meyer、Wallachはパラメータ化された量子回路のエンタングリング容量を回路から生成し得る状態のMeyer-Wallach測度の平均と定義づけています。

```
For example, consider the following multi-qubit parameterized circuits:
Circuit A has no entangling operations, that is, no two-qubit gates, so it has no entangling capability. Thus, this circuit would produce states that have Meyer-Wallach measures of 0, leading to an averaged value of 0. Circuit B has several two-qubit gates, so it can generate quantum states with some entanglement. Therefore, the average Meyer-Wallach measure would be greater than 0.

Using these two parameterized quantum circuit descriptors, expressibility and entangling capability, we can identify circuits that have limited capabilities. We expect that limited circuits are poor candidates for quantum machine learning applications. Reference 2 investigates this for the variational quantum classifier; and for its datasets and parameterized circuits, it finds that there is a strong correlation between classification accuracy and expressibility, and a weak correlation between classification accuracy entangling capability.
```
例えば以下のような複数量子ビットのパラーメータ化回路を考えてみます。
回路Aはもつれ操作、つまり2量子ビットゲートは存在せず、エンタングリング容量はありません。つまりこの回路はMeyer-Wallach測度が0の状態を生成し、その平均値は0ということになります。回路Bはいくつかの2量子ビットが存在するためいくらかのエンタングルメントを持つ状態が生成される可能性があります。つまりMeyer-Wallach測度がの平均値は0より大きいということになります。

表現能力とエンタングル能力という2つのパラメータ化された回路の記述子を用いて回路の能力の限界を知ることが出来ます。限界値の低い回路は量子機械学習への応用として向いてはいません。参考文献2では変分量子分類にてこのことについて研究がされていて、表現能力と分類の精度に強い相関が存在すること、また分類の精度ともつれ能力に弱い相関が存在することが明らかにされている。

```
## Hardware efficiency
In this era of near term quantum computing, where devices have limited qubit connectivity, coherence times and gate fidelities, the depth of the circuits we run directly affects the accuracy of our results, since there is limited error correction or mitigation.

Reference 3 introduces a class of hardware efficient parameterized circuits to accommodate device constraints. The common trait of these circuits is the use of a limited set of quantum gates as well as a particular qubit connection topology. The gate set usually consists of one two-qubit entangling gate and up to three single-qubit gates. The circuit is then constructed from blocks of single-qubit gates and entangling gates, which are applied to multiple or all qubits in parallel. One sequence of a single-qubit and an entangling block is called a layer, and the parameterized circuit generally has multiple layers.

Circuit B depicted in the 'Entangling Capability' section (also shown below) is an example of a single layer hardware efficient parameterized circuit.
```
## ハードウェア効率
近年の量子コンピューティングの時代では、デバイスは量子ビットの接続に制限が存在しているため、コヒーレンス時間やゲートのフィデリティ、回路の深さが結果の精度に直結します。というのも限られたエラー訂正や緩和の方法しか存在しないからです。

参考文献3ではデバイスの制約に合わせて効率的にパラメータ化した回路群を提唱しています。回路は共通して量子ビットの結合トポロジーだけでなく、量子ゲートも特定のものに絞っているという特徴があります。量子ゲートはたいてい1つの2量子エンタングリングゲートと最大3つの1量子ビットゲートで構成されます。そして回路は1量子ビットゲートとエンタングリングゲートのブロックで組み立てられ、複数または全ての量子ビットに対して並列に適用されます。1量子ビットゲートのブロックとエンタングリングゲートのブロックの並びはレイヤーと呼ばれ、一般にパラメータ化された回路は複数のレイヤーを持ちます。

「エンタングリング容量」の節で登場した回路Bはハードウェア向けに効率化されたレイヤーを1つもつパラメータ化回路の一例です。

```
# Parameterized quantum circuits for machine learning
In quantum machine learning, parameterized quantum circuits tend to be used for two things:

- To encode data, where the parameters are determined by the data being encoded
- As a quantum model, where the parameters are determined by an optimization process.
Here we will give some examples of how to build parameterized quantum circuits used in the quantum machine learning literature using Qiskit.

In Reference 4, the authors introduce the following parameterized circuit, used to encode data, which we will study in detail later:

which contains layers of Hadamard gates interleaved with entangling blocks, . This unitary was chosen because it is classically difficult to compute, but tractable on near term hardware. Within the entangling blocks, : 
 denotes the Pauli matrices, the index  describes connectivities between different qubits: 
, and the data mapping function 
 is
 Specifically, they use 　, which in Qiskit is the ZZFeatureMap circuit:
```
# 機械学習におけるパラメータ化された量子回路
量子機械学習においてパラメータ化された量子回路は以下の2つの用途に用いられることが多いです。
- データのエンコード：パラメータはエンコードされるデータによって決定される
- 量子モデル：パラメータは最適化処理によって決定される
それではQiskitを用いた量子機械学習においてパラメータ化された回路をどのように構築するかの例をいくつか紹介していきます。

参考文献4では著者は以下のパラメータ化された回路を導入しています。これはデータをエンコードしていて、詳細は後ほど学習します。

 アダマールゲートともつれ操作のTODO:の層が交互に含まれています。このユニタリ操作が採用されたのは古典コンピュータでは計算が難しく、かつNear-termのハードウェアで扱えるからです。

```
In Reference 4, the authors also use a hardware efficient circuit as a quantum model, consisting of alternating layers of single-qubit rotation gates, followed by two-qubit gates. In particular, they use -and--rotation gates, and controlled- gates, which we can build using the TwoLocal circuit:

The TwoLocal circuit in Qiskit can create many parameterized circuits, such as circuit 13 in Reference 1:

Qiskit's NLocal circuit can also create more general parameterized circuits with alternating rotation and entanglement layers.

Here is a NLocal circuit, with a rotation block on 2 qubits and an entanglement block on 4 qubits using linear entanglement:
```
参考文献4では著者は量子モデルとしてハードウェアの効率がよい回路も使用しています。その回路は1量子ビットの回転ゲートに2量子ビットゲートが続く層から構成されています。特にy軸回転、z軸回転、制御z軸回転ゲートを使用していて、これはTwoLocal回路利用することで私たちも構築することが出来ます。

QiskitのTwoLocal回路は様々なパラメータ化された回路を生成することが可能で、例えば参考文献1の回路13も生成することが出来ます。

Qiskitでは回転ともつれの層を変更したNLocalを使用することでより一般的なパラメータ化された回路を生成することも可能です。

こちらがNLocalの回路で2量子ビットの回転ブロックと、線形もつれを用いた4量子ビットのもつれ操作のブロックが存在しています。