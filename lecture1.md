---
marp: true
header: Tensor network representation learning for physics
footer: ©2024 H. Shinaoka
# Page number
paginate: true
#style: ./style.css
---

<style>

section::after {
  content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);
}

img[alt~="center"] {
  display: block;
  margin: 0 auto;
}

h1 {
  position: absolute;
  top: 1.5em;
  width: 100%;
  text-align: left;
}

</style>



$$
\newcommand{\iv}{{\mathrm{i}\nu}}
\newcommand{\iwk}{{\mathrm{i}\bar{\omega}_k}}
\newcommand{\ivk}{{\mathrm{i}\bar{\nu}_k}}
\newcommand{\tauk}{\bar{\tau_k}}
\newcommand{\ii}{{\mathrm{i}}}
\newcommand{\iw}{{\mathrm{i}\omega}}
\newcommand{\wmax}{{\omega_\mathrm{max}}}
\newcommand{\dd}{{\mathrm{d}}}
\newcommand{\tauk}{{\bar{\tau}_k}}
\newcommand{\wk}{{\bar{\omega}_k}}
\newcommand{\vk}{{\bar{\nu}_k}}
\newcommand{\hatFmat}{\hat{\mathbf{F}}}
\newcommand{\Fmat}{{\mathbf{F}}}
\newcommand{\cR}{{\mathcal{R}}}
\newcommand{\scR}{{\scriptstyle \mathcal{R}}}
\newcommand{\sscR}{{\scriptscriptstyle \mathcal{R}}}

\newcommand{\cN}{{\mathcal{N}}}
\newcommand{\scN}{{\scriptstyle \mathcal{N}}}
\newcommand{\sscN}{{\scriptscriptstyle \mathcal{N}}}

\newcommand{\cL}{{\mathcal{L}}}
\newcommand{\scL}{{\scriptstyle \mathcal{L}}}
\newcommand{\sscL}{{\scriptscriptstyle \mathcal{L}}}

\newcommand{\sigmaL}{\sigma_{\!\scL}}

\def\bsigma{{\boldsymbol{\sigma}}} 
\def\bx{{\boldsymbol{\mathrm{x}}}} 
\def\sigmas{\sigma}

$$

Tensor network representation learning for physics
===

### Hiroshi SHINAOKA (Saitama University)
<br>

- 専門: 量子多体理論、第一原理計算、幾何学的フラストレート磁性･･･
- 興味: 情報縮約技術と場の量子論 (計算量子物理のフロンティアを拡げたい！)


<center>
<span style="font-size: 0.8em">This lecture is based on Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.</span>
</center>

---
# 今日の講義

### 目標
データ/関数をテンソルネットワークで表現し, その特徴を抽出する方法を学ぶ.

* 前半
   - Tensor networkの復習
   - Natural & quantics tensor network representation (TNR) of a function
* 後半
   - Learning TNR: Tensor Cross Interpolation (TCI)
   - Computation with TNRs
   - Juliaライブラリで実践してみる (30-60分ぐらいを予定)


---
# 参考資料

### 事前知識
- テンソルネットワーク: [昨年の大久保さんの講義の最初30分](https://www.youtube.com/watch?v=i2wsatfsogI)
- Julia予備知識: 初日の講義の寺崎さんの講義.

### 予習資料
- 「Quantics tensor train に基づく多スケール時空仮説と場の量子論」、品岡寛、村上雄太、野垣康介、櫻井理人、日本物理学会誌2024年2月号、**2.1, 2.2章** [PDF](https://shinaoka.github.io/assets/qtt_jps_202402.pdf)

---
# より詳しい資料

* QTT: [B. N. Khoromskij, Tensor Numerical Methods in Scientific Computing](https://www.amazon.co.jp/dp/3110370131?ref_=cm_sw_r_cp_ud_dp_C1N79TD92FAJKEQEMXEN)
* C++/Python/Juliaライブラリ、TCIの新しいアルゴリズム (self-contained): Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.

---
# ハンズオン準備

ハンズオン参加希望者は, 講義までに以下の準備をお願いします.
会場のインターネット回線経由でインストールしないこと！

1. Juliaのインストール
2. ライブラリのインストール, 以下のコマンドを実行 (PowerShell, Terminal等で実行)

```julia
julia -e 'using Pkg; Pkg.add(["Example"])'
julia -e 'using Pkg; Pkg.Registry.add(RegistrySpec(url="git@gitlab.com:tensors4fields/tensors4fieldsregistry.git"))'
julia -e 'using Pkg; Pkg.add(["QuanticsTCI", "TensorCrossInterpolation", "QuanticsGrids", "TCIITensorConversion", "Plots", "PythonPlot", "LaTeXStrings"])'
````


---
# 具体例

xfac論文から、綺麗な具体例をいくつか示す。
流体、場の量子論の図も見せて、モチベートする。

---
# テンソルネットワークとは？ (復習)
<center>
<span style="font-size: 0.7em">Taken from Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation</span>
</center>


![center height:450px bottom](fig/standardTTtoolboxXavierVersion.png)

---

---


---
# 関数をテンソル化する2つの方法

1. Natural tensor representation
1. Quantics representation



---
# Natural tensor representationとは

$\cN$変数関数 ($\bx \in \mathbb{R}^\scN$):

$$
f\bigl(\bx(\bsigma)\bigr) = f\bigl(x_1(\sigmas_1), \cdots, x_\scL (\sigmas_\scL)\bigr)=
$$

![center width:500px](fig/fnatural.png)

離散グリッド:
$$
x_1: \{x_1(1), \cdots, x_1(d_1)\}, \cdots,
x_\scN: \{x_\scN(1), \cdots, x_\scN(d_\scN)\}
$$

ここで, $d_\ell$は, $\ell$番目の変数に対する離散化グリッドの大きさ.

**欠点**: 局所構造や, 桁違いに違う長さスケールが共存 $\rightarrow$ $d_\ell$が増大

---
# Quantics representation

桁違いに違う長さスケールが共存に有効. 

**アイデア**: 離散グリッドのインデックスをさらに「量子化」(quantics)する.

---
# 10進法

例えば, 3桁の非負整数を考えてみよう ($\mathscr{R}=3$):
$i = 000, 001, 002, \cdots, 010, 011, \cdots, 100, 101, \cdots, 999.$

1つの数字を異なる桁を表す複数の数字の組で表現する.
$i = a_1 \times \textcolor{red}{10^2} + a_2 \times \textcolor{red}{10^1} + a_3 \times \textcolor{red}{10^0} = (a_1 a_2 a_3)_{10}$.

ただし, $a_r \in \{0, 1, \cdots, 9\}$


---
# なぜ10進法が便利なのか？

桁数$\cR$を増やせば, 表現可能な整数の最大値 ($10^\scR-1$)を指数的に大きく出来る！

$i = a_1 \times \textcolor{red}{10^{\scR-1}} + \cdots + a_r \times \textcolor{red}{10^{\scR-r}} + \cdots + a_\scR \times \textcolor{red}{10^0} = (a_1 a_2 \cdots a_\scR)_{10}$.

**注目点** 底10と同じ数だけの文字があれば良い！

---
# 2進法

底10が一般的なのは, 手の指の数が10本だから.
コンピュータの内部表現は2進数 (0, 1が電気信号のON, OFFと対応).

$i = a_1 \times \textcolor{red}{2^{\scR-1}} + \cdots + a_r \times \textcolor{red}{2^{\scR-r}} + \cdots + a_\scR \times \textcolor{red}{2^0} = (a_1 a_2 \cdots a_\scR)_2$.

ただし, $a_r \in \{0, 1\}$.

<!--
Quanticsでは通常2進数を使うが, 底は任意 (一部の特殊例, フラクタルなどを除く).
-->

---
# Quantics representation (1変数)[1]

関数: $f(x),~x \in [x_\mathrm{min}, x_\mathrm{max}]$

等間隔グリッド (大きさ$M=2^\scR$) 上で離散化:


![center width:800px](fig/1dgrid.png)

---
# Quantics representation (1変数)[2]


![center width:800px](fig/1dquantics.png)

$2^\scR$の大きな足を, $\cR$個の大きさ2の足に分割!

<!--
 $M=2^\cR$ ($m=0, 1, 2, \cdots, M-1$):
関数$f(x)$は, 大きさ$2^\scR$の1次元データ $F_m$に離散化

$$
m(\bsigma) = m(\sigma_1, \sigma_2, \cdots, \sigma_\scR) = \sum_{r=1}^\scR \sigma_r 2^{\scR -r } = (\sigma_1 \cdots \sigma_\scR)_2.
$$

$\delta = (x_\mathrm{max} - x_\mathrm{min})/M$

$\cR$個の小さな変数$\sigma_r~\in \{0, 1\}$を使う.

\textcolor{red}{ここに図を入れる.}
-->

---
# Quantics representation ($\cN$変数)

各変数を$(2^\scR)$個に離散化 $\rightarrow$ $n$番目変数の量子化: $m_n = (\sigma_{n1} \cdots \sigma_{n \scR})_2$

![center width:1200px](fig/quantics2.png)

通常, 強くエンタングルしている同じ長さスケールの変数を隣同士に並べることが多い.

---
# TT unfolding - natural tensor representation -

$\cN$変数の分離に対応: $f(x_1,...,x_\scN) \approx M_1(x_1)M_2(x_2)...M_\scN(x_\scN)$

![center width:1200px](fig/TT.png)

<br>

Superfast summation: $\int \! d^{\scN} \bx f(\bx) \approx  \int dx_1\ M_1(x_1) \int dx_2\  M_2(x_2)... \int dx_\scN\  M_\scN(x_\scN).$

---
# TT unfolding - quantics representation -

異なるスケール間の分離に対応

![center width:900px](fig/schematic_QTT.png)

積分: $\int dx f(x) \approx 2^{-\scR} (\sum_{\sigma_1} M(\sigma_1)) ... (\sum_{\sigma_\scR} M(\sigma_\scR)) + O(2^{-2\scR})$

![center width:900px](fig/TTsum.png)
$\cR$に対して線形な演算量 $O(\chi^2 \cR)$, 指数的に小さい離散化誤差

---
# Superfast computation in QTT

Tensor Train Operator (TTO):

![center width:1100px](fig/schematic_QTTO.png)

複雑な演算 (畳み込み積分, フーリエ変換)は後半の講義で紹介

---
# 演習問題: Natural tensor representation

以下の3変数関数がボンド1次元のTTで表現できることを示してみましょう.
$$
f(x, y, z) = x y z
$$


---
# 演習問題: Quantics representation (1)

$f(x) = e^{x}~(x\in [0, 1])$は, ボンド次元1のQTT表現を持つことを示してみましょう.

---
# 演習問題: Quantics representation (2)

$f(x) = e^{x + y}~(x, y\in [0, 1])$の, ボンド次元1のQTT表現を求めてみましょう.




---
# Learning TNR: Tensor Cross Interpolation (TCI)

与えられた関数から,
* 低ランク表現の有無を判定したい,
* あるなら(半)自動的に構築したい.

**TCIは, そのような目的に使える手法の一つ.**


### 注意
$N$次元テンソル$T$も関数と見なせる:
$f: \mathbb{R}^N \to \mathbb{C}, \quad f(i_1, i_2, \dots, i_R) = T[i_1, i_2, \cdots, i_R]$

---
# Example

$$
\begin{align}
&f(x) = \cos\left(\frac{x}{B}\right) \cos\left(\frac{x}{4\sqrt{5}B}\right)  e^{-x^2} + 2e^{-x}~(B=2^{-30}),\nonumber\\
& \int_0^{\ln (20)} \mathrm{d} x f(x) \approx \frac{19}{10} 
\end{align}
$$

8706 samples (1 sample per 59000 oscillations)

![bg right width:500px](fig/oned_cosine_gaussian_outline.png)

<center>
<span style="font-size: 0.7em">Adapted from M. K. Ritter, Y. N. Fernández, M. Wallerberger, J. von Delft, H. Shinaoka, X. Waintal, PRL <b>132</b>, 056501 (2024).
</center>



---
# Matrix Cross Interpolation (MCI)

![center width:1200px](fig/MCI.png)

* 選択された色付きの行と列で, 両辺の要素は必ず一致
* ピボットの数 = 行列のランクの時は, 両辺は全体で一致
* 良いピボットを選ぶヒューリスティックス法 (e.g., maxvol algorithm)

---
# MCIの動作

![center width:720px](fig/MCI_pivot_search.png)


---
# Tensor Cross Interpolation (TCI) in a nutshell

テンソルの要素を適応的に探索し, 低ランクTT表現を求める. 

* **必要条件**  指定されたインデックスでのテンソルの値が計算可能 $f: \mathbb{R}^N \to \mathbb{C}$
* **特徴** テンソル全体の要素を読み出し不要 (関数評価回数 $\propto \chi^2 \cL$)
* **デメリット** ヒューリスティクスな手法, 一般的な関数に対しては, 成功の保証無

TensorCrossInterpolation.jl: LU分解に基づくピボット選択 + グローバル探索


---
# 特異値によるテンソル分解とTCIの比較 (玄人向け)

### 特異値分解
* フロベニウスノルム $\sqrt{\sum_i|x_i|^2}$の意味で最適なランクが決定.
* 全要素読み出しが必要.


### TCI/MCI
* **最大値ノルム** $\max_i |x_i|$の意味で**準最適**なランクを求める.
* 全要素読み出しが不要.


---
# フーリエ変換カーネルのQTCI

何枚かのスライドを使って, QTTによる演算例を示す.
$f(x, y) = e^{x + y}$の場合を考える.
量子フーリエ変換の例も示す.


---
# Computation with TNRs

* 要素積
* 「行列積」
* 量子フーリエ変換