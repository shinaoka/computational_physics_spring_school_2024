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

\def\br{{\boldsymbol{r}}} 

\newcommand{\FT}{{\scriptscriptstyle \mathrm{FT}}}
\newcommand{\hf}{f^\FT}   %Fourier transform of \bff
\newcommand{\hF}{F^\FT}

\newcommand{\mI}{\mathcal{I}}
\newcommand{\mJ}{\mathcal{J}}
\newcommand{\aI}{\mathbbm{I}}
\newcommand{\aJ}{\mathbbm{J}}

\newcommand{\ellp}{{\ell'}}
\newcommand{\ellpminusone}{{\ellp \hspace{-0.2mm}-\hspace{-0.2mm} 1}}
\newcommand{\ellminusone}{{\ell \hspace{-0.2mm}-\hspace{-0.2mm} 1}}
\newcommand{\ellplusone}{{\ell  \hspace{-0.2mm}+\hspace{-0.2mm} 1}}
\newcommand{\ellpplusone}{{\ellp  \hspace{-0.2mm}+\hspace{-0.2mm} 1}}
\newcommand{\ellplustwo}{{\ell  \hspace{-0.2mm}+\hspace{-0.2mm} 2}}
\newcommand{\ellpplustwo}{{\ellp  \hspace{-0.2mm}+\hspace{-0.2mm} 2}}
\newcommand{\ellminustwo}{{\ell \hspace{-0.2mm}-\hspace{-0.2mm} 2}}
$$

Tensor network representation learning for physics
===

### Hiroshi SHINAOKA (Saitama University)
<br>

- 専門: 量子多体理論、第一原理計算、幾何学的フラストレート磁性･･･
- 興味: 情報縮約技術と場の量子論 (計算量子物理のフロンティアを拡げたい！)


<center>
<span style="font-size: 0.9em; color: red">This lecture is based on Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.</span>
</center>


---
# 自己紹介 [https://shinaoka.github.io](https://shinaoka.github.io)

* 経歴
  - 博士 (工学) 2009年3月@東大物工
  - ポスドク 2009年3月〜2015年9月 (東大、産総研、チューリッヒ連邦工科大)
  - 研究室主催＠埼玉大 2015年10月〜
* 専門
  - 量子多体理論、第一原理計算、幾何学的フラストレート磁性･･･
  - 計算物理バックエンドの開発に興味<br>ALPS量子モンテカルロコード、スパースモデリング･･･
* 海外との連携
* 学術変革領域B「量子古典融合アルゴリズムが拓く計算物質科学」

---
# 今日の講義

### 目標
データ/関数をテンソルネットワークで表現し, その特徴を抽出する方法を学ぶ.
Juliaをつかって自分で作った関数をテンソルネットワークで表現してみる.

* 前半
   - Tensor networkの復習
   - Natural & quantics tensor network representation (TNR) of a function
* 後半
   - Learning TNR: Tensor Cross Interpolation (TCI)
   - Computation with TNRs
   - Juliaライブラリで実践してみる (30-60分ぐらいを予定)


---
# 予習資料

講義は以下の予習資料を前提として進めます.

- テンソルネットワーク: [昨年の大久保さんの講義の最初30分](https://www.youtube.com/watch?v=i2wsatfsogI)
- [特異値分解 (singular value decomposition, SVD)](https://ja.wikipedia.org/wiki/特異値分解)
- (ハンズオン参加希望者) Julia予備知識: 初日の講義の寺崎さんの講義.

---
# 参考資料
以下の資料は予習には必要ありませんが, 興味がある方は参考にしてください.

- 「Quantics tensor train に基づく多スケール時空仮説と場の量子論」、品岡寛、村上雄太、野垣康介、櫻井理人、日本物理学会誌2024年2月号、**2.1, 2.2章** [PDF](https://shinaoka.github.io/assets/qtt_jps_202402.pdf)


### ハンズオン資料
- (作成中) [Tensors4FieldsのWebサイト](https://gitlab.com/tensors4fields)
- (作成中) [Tensors4Fieldsのサンプル集](https://tensors4fields.gitlab.io/T4FExamples.jl/dev/index.html)

---
# より詳しい資料

* QTT: [B. N. Khoromskij, Tensor Numerical Methods in Scientific Computing](https://www.amazon.co.jp/dp/3110370131?ref_=cm_sw_r_cp_ud_dp_C1N79TD92FAJKEQEMXEN)
* C++/Python/Juliaライブラリ、TCIの新しいアルゴリズム (self-contained style): Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.
* H. Shinaoka, M. Wallerberger, Y. Murakami, K. Nogaki, R. Sakurai, P. Werner, and A. Kauch, PRX **13**, 021015 (2023).
* M. K. Ritter, Y. N. Fernández, M. Wallerberger, J. von Delft, H. Shinaoka, X. Waintal, PRL **132**, 056501 (2024).

---
# ハンズオン準備

ハンズオン参加希望者は, 講義までに以下の準備をお願いします.
会場のインターネット回線経由でインストールしないこと！

1. Juliaのインストール
2. ライブラリのインストール, 以下のコマンドを実行 (PowerShell, Terminal等で実行)

```julia
julia -e 'using Pkg; Pkg.add(["Example"])'
julia -e 'using Pkg; Pkg.Registry.add(RegistrySpec(url="https://gitlab.com/tensors4fields/tensors4fieldsregistry.git"))'
julia -e 'using Pkg; Pkg.add(PackageSpec(name="QuanticsTCI", version="0.4.3"))'
julia -e 'using Pkg; Pkg.add(PackageSpec(name="QuanticsGrids", version="0.2"))'
julia -e 'using Pkg; Pkg.add(PackageSpec(name="TensorCrossInterpolation", version="0.8"))'
julia -e 'using Pkg; Pkg.add(PackageSpec(name="TCIITensorConversion", version="0.1"))'
julia -e 'using Pkg; Pkg.add(["Plots", "PythonPlot", "LaTeXStrings"])'
````


---
# tensor4all

![bg width:1000px](fig/tensor4all.svg)


---
# 場の理論

場とは時空に依存した関数: $F(\br_1, \br_2, \cdots, \br_N)$.

* $\br_i \equiv (\vec{r}_i, t_i)$ ($i=1, \cdots, N$)
* $N$は粒子の数 or 考える散乱過程の次数.

**例**: 量子多体系のグリーン関数, ファインマンダイアグラム, 流体の速度場

**必要な演算**: 積分, 和, フーリエ変換, 畳み込み積分, 偏微分・常微分方程式の解など

---
# 場をどう記述するか？

* 連続変数を離散化する: グリッドを作る, adaptive mesh refinement
* ニューラルネットワーク表現 (表現能力高)
* **テンソルネットワーク表現** (構造化されたモデルによる高速な演算)

**最近の重要な進展**:

* 様々な「場」に潜む低ランク構造の発見 (quantics表現)
* テンソルネットワークの「能動学習」アルゴリズムの開発 (tensor cross interpolation)

---
# Quantics Tensor Train (QTT) + 流体力学

Navier–Stokes equations, 乱流, 空間依存性のQTT表現


![bg right width:600px](fig/multiscale2.png)


<center>
<span style="font-size: 0.7em">
N. Gourianov <i>et al.</i>, Nat. Comput. Sci. <b>2</b>, 30 (2022).
</span>
</center>


---
# Quantics Tensor Train (QTT) + 場の量子論

* 桁違いの長さスケールが存在する時空依存性の圧縮
* 場の演算: 畳み込み, フーリエ変換など

![bg right width:400px](fig/multiscale.png)


<center>
<span style="font-size: 0.7em">
H. Shinaoka <i>et al.</i>, PRX <b>13</b>, 021015 (2023).
</span>
</center>

---
# Learning Feynmann diagrams by tensor networks

<br>
ファインマンダイアグラムの数値積分

* 量子モンテカルロ法: 負符号問題 (統計誤差発散)
* Tensor Cross Interpolation (TCI): 関数に潜む「低ランク性」→負符号問題に影響されない

![bg right width:500px](fig/yuriel2022.png)

<center>
<span style="font-size: 0.7em">
Y. N. Fernández <i>et al.</i>, PRX <b>12</b>, 041018 (2022).
</span>
</center>



---
# Quantics Tensor Cross Interpolation (QTCI)

指数的解像度 + TCIの高速学習

$\rightarrow$ 波数積分などへの応用

![bg right width:500px](fig/ritter2024.png)

<center>
<span style="font-size: 0.7em">
M. K. Ritter, Y. N. Fernández, M. Wallerberger, J. von Delft, H. Shinaoka, X. Waintal, PRL <b>132</b>, 056501 (2024).
</span>
</center>

---
# Demonstration of QTCI


$$
\begin{align}
f(x,y) &= 1+e^{-0.4 \left( x^{2} + y^{2} \right)} + \sin \left( x y \right)  e^{-x^{2}} +\cos \left( 3 x y \right) e^{-y^{2}} + \cos \left( x + y \right) \nonumber \\
& + 0.05 \cos \left[ 10^2 \cdot \left( 2 x - 4 y \right) \right] + 5\cdot 10^{-4} \cos \left[ 10^{3} \cdot \left( -2 x + 7 y \right) \right] + 10^{-5} \cos \left( 2\cdot 10^{8} x \right) 
\end{align}
$$

![width:1000px](fig/2D_quantics_zoom.png)

Dense grid ($10^{12}\times 10^{12}$, $10^{13}$ TB) $\rightarrow$ Quantics grid ($10^5$ floats, 1 MB)


---
# Rank of a matrix (復習)

行列$A$のランク$\mathrm{rank}(A)$の定義:

* $A$の$r$個の行/列ベクトルが線形独立 $\rightarrow$ $\mathrm{rank}(A)$ = $r$
* $A = U \Sigma V^T$ (Singular Value Decomposiiton/SVD) $\rightarrow$ $\mathrm{rank}(A)$ = $\mathrm{rank}(\Sigma)$=非0の特異値の数

行列の低ランク構造 $r$によって, $A$を次のようにSVDできる:
$$
A = U \Sigma V^T \approx \sum_{i=1}^r \sigma_i u_i v_i^T.
$$

SVDの他に, QR分解, LU分解などがある.

---
# Low-rank approximation of a matrix (復習)

### 定義
$$
\text{minimize}_{\tilde A} \|A - \tilde A\| \quad \text{subject to} \quad \text{rank}(\tilde A) = r' < r~(\equiv \mathrm{rank}(A)).
$$
  
### ノルム$\| \cdots \|$の選択
* フロベニウスノルム $\|B\|_\mathrm{F} = \sqrt{\sum_{ij}|B_{ij}|^2}$ $\rightarrow$ 特異値分解＋打ち切りが最適
* 最大値ノルム $\|B\|_\mathrm{max} = \max_{ij} |B_{ij}|$ $\rightarrow$ rank-revealing LU分解が使える (後述のTCIのバックエンド)



---
# テンソルネットワーク (復習)
<center>
<span style="font-size: 0.7em">Taken from Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation</span>
</center>


![center height:450px bottom](fig/standardTTtoolboxXavierVersion.svg)

---
# Black page for drawing


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

![center width:500px](fig/fnatural.svg)

離散グリッド:
$$
x_1\rightarrow \{x_1(1), \cdots, x_1(d_1)\}, \cdots,
x_\scN\rightarrow \{x_\scN(1), \cdots, x_\scN(d_\scN)\}
$$

ここで, $d_\ell$は, $\ell$番目の変数に対する離散化グリッドの大きさ.

**欠点**: 局所構造や, 桁違いに違う長さスケールが共存 $\rightarrow$ $d_\ell$が増大

---
# Quantics representation

桁違いに違う長さスケールが共存に有効. 

**アイデア**: 離散グリッドのインデックスをさらに「量子化」(quantics)する.

I. V. Oseledets, Doklady Math. **80**, 653 (2009), B. N. Khoromskij, Constr. Approx. **34**, 257 (2011)

---
# 10進法

例えば, 3桁の非負整数を考えてみよう ($\cR=3$):
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


![center width:800px](fig/1dgrid.svg)

---
# Quantics representation (1変数)[2]


![center width:900px](fig/1dquantics.svg)

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

![center width:1200px](fig/quantics2.svg)

通常, 強くエンタングルしている同じ長さスケールの変数を隣同士に並べることが多い.

---
# TT unfolding - natural tensor representation -

$\cN$変数の分離に対応: $f(x_1,...,x_\scN) \approx M_1(x_1)M_2(x_2)...M_\scN(x_\scN)$

![center width:1200px](fig/TT.svg)

<br>

Superfast summation: $\int \! d^{\scN} \bx f(\bx) \approx  \int dx_1\ M_1(x_1) \int dx_2\  M_2(x_2)... \int dx_\scN\  M_\scN(x_\scN).$

---
# TT unfolding - quantics representation -

異なるスケール間の分離に対応

![center width:900px](fig/schematic_QTT.svg)

積分: $\int dx f(x) \approx 2^{-\scR} (\sum_{\sigma_1} M(\sigma_1)) ... (\sum_{\sigma_\scR} M(\sigma_\scR)) + O(2^{-2\scR})$

![center width:900px](fig/TTsum.svg)
$\cR$に対して線形な演算量 $O(\chi^2 \cR)$, 指数的に小さい離散化誤差

---
# Superfast computation in QTT

Tensor Train Operator (TTO):

![center width:1100px](fig/schematic_QTTO.svg)

複雑な演算 (畳み込み積分, フーリエ変換)は後半の講義で紹介

---
# 演習問題: Natural tensor representation

3変数関数$f(x, y, z) = x y z$が, ボンド1次元のTTで表現できることを示してみましょう. ただし, natural tensor representationを使うこと.


---
# 演習問題: Quantics representation (1)

$f(x) = e^{x}~(x\in [0, 1])$は, ボンド次元1のQTT表現を持つことを示してみましょう.


---
# 演習問題: Quantics representation (2)

$f(x, y) = \delta_{xy}$の, ボンド次元1のQTT表現を求めてみましょう ($x, y = 0, 1, 2, \cdots, 2^\scR-1$).


---
# 演習問題: Quantics representation (3)

$f(x, y) = e^{x + y}~(x, y\in [0, 1])$の, ボンド次元1のQTT表現を求めてみましょう.




---
# Learning TNR: Tensor Cross Interpolation (TCI)

与えられた関数から,
* 低ランク表現の有無を判定したい,
* あるなら(半)自動的にTTを構築したい.

**TCIは, そのような目的に使える能動学習手法の一つ.**


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

![bg right width:500px](fig/oned_cosine_gaussian_outline.svg)

<center>
<span style="font-size: 0.7em">Adapted from M. K. Ritter, Y. N. Fernández, M. Wallerberger, J. von Delft, H. Shinaoka, X. Waintal, PRL <b>132</b>, 056501 (2024).
</center>



---
# Matrix Cross Interpolation (MCI)

![center width:1200px](fig/MCI.svg)

* 選択された色付きの行と列で, 両辺の要素は必ず一致
* ピボットの数 = 行列のランクの時は, 両辺は全体で一致
* 良いピボットを選ぶヒューリスティックス法 (e.g., maxvol algorithm). 基本的には, $|A - \tilde A|_\mathrm{max}$を最小化するように選ぶ.

---
# MCIの動作

![center width:720px](fig/MCI_pivot_search.svg)


---
# MCI $\rightarrow$ TCI

![center width:1200px](fig/schematic_tci_revised.svg)

$[T_\ell]^{\sigma_\ell}_{i_{\ell-1} j_{\ell+1}} \equiv F_{i_{l-1}\sigma_\ell j_{l+1}},~[P_\ell]^{\sigma_\ell}_{i_{\ell} j_{\ell+1}} \equiv F_{i_\ell j_{l+1}}$

内挿の精度は, ピボットの数や選び方に強く依存.

---
# Nesting condition

![center width:1200px](fig/schematic_tci_revised.svg)

$[T_\ell]^{\sigma_\ell}_{i_{\ell-1} j_{\ell+1}} \equiv F_{i_{l-1}\sigma_\ell j_{l+1}},~[P_\ell]^{\sigma_\ell}_{i_{\ell} j_{\ell+1}} \equiv F_{i_\ell j_{l+1}}$


$T_\ell$に対するpartial nesting condition:
* 全ての$\ellp \le \ell-1$に対して, $\mI_\ellpminusone \!\times\! \{\sigma_\ellp\} \subseteq \mathcal{I}_\ellp$が成立
* 全ての$\ellp \ge \ell+1$に対して, $\{\sigma_\ellp\} \!\times\! \mJ_\ellpplusone \subseteq \mathcal{J}_\ellp$が成立.

$\Longrightarrow$ 内挿$\tilde F$は, $T_\ell$に含まれる全ての要素において, $F$と一致.

---
# Pivot selection

色んな方法があります.

* TCI**1**アルゴリズム: 例えば, Y. N. Fernández <i>et al.</i>, PRX <b>12</b>, 041018 (2022)にレビューされている.
* TCI**2**アルゴリズム: LU分解ベースの新しい方法. Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.

$\texttt{TensorCrossInterpolation.jl}$の「デフォルト」は, TCI**2**とグローバルピボット探索アルゴリズム

---
# Tensor Cross Interpolation (TCI)の特徴

テンソルの要素を適応的に探索し, 低ランクTT表現を求める. 

* **必要条件**  指定されたインデックスでのテンソルの値が計算可能 $f: \mathbb{R}^N \to \mathbb{C}$
* **特徴** テンソル全体の要素を読み出し不要 (関数評価回数 $\propto \chi^2 \cL$)
* **デメリット** ヒューリスティクスな手法, 一般的な関数に対しては, 成功の保証無
* **機械学習との関係**
  - 能動学習 (active learning)の一種
  - 構造化されたモデルを使う $\rightarrow$ 構築後の計算が効率的 (e.g.,積分, フーリエ変換)



---
# 特異値によるテンソル分解とTCIの比較 (玄人向け)

### 特異値分解
* フロベニウスノルム $\sqrt{\sum_i|x_i|^2}$の意味で最適なランクが決定.
* 全要素読み出しが必要.


### TCI/MCI
* **最大値ノルム** $\max_i |x_i|$の意味で**準最適**なランクを求める.
* 全要素読み出しが不要.


---
# フーリエ変換カーネル

$$
\hf_k = \sum_{m=0}^{M-1}   T_{km} f_m , \qquad 
T_{km} =  \tfrac{1}{\sqrt{M}}  e^{- i 2 \pi k \cdot m /M}
$$
$$
m(\bsigma) = (\sigma_1 \cdots \sigma_\scR)_2,~k(\bsigma') = (\sigma_1' \cdots \sigma_\scR')_2
$$
![center width:900px](fig/schematic_qft_before_unfolding.svg)
![center width:900px](fig/schematic_qft.svg)

変換前後でインデックスが逆順で低ランク [Woolfe2017](https://www.rintonpress.com/journals/doi/QIC17.1-2-1.html), [Shinaoka2024](https://journals.aps.org/prx/abstract/10.1103/PhysRevX.13.021015), [Chen2024](https://doi.org/10.1103/PRXQuantum.4.040318)

---
# フーリエ変換カーネルのQTCI

![center width:900px](fig/qft-bonddimensions.svg)

<center>
<span style="font-size: 0.8em">Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.</span>
</center>

QTCI経由では無く, 量子フーリエ変換の量子回路経由で作ることも出来ます [Chen2024](https://doi.org/10.1103/PRXQuantum.4.040318).

---
# Computation with quantics

* 行列積: $C(x, x'') = \int \mathrm{d}x' A(x, x') B(x', x'')$
* フーリエ変換
* 畳み込み (=要素積＋フーリエ変換)
* 要素積: $h(x) = f(x) g(x)$をQTCI推定する.

$\texttt{Quantics.jl}$に実装されてます (まだ実験的なライブラリ).

---
# 低ランク構造どう探すか？: フローチャート

低ランク構造があるかどうかは, 一般的には難しい.

データ・関数 $\rightarrow$ (Quantics) TCI $\rightarrow$ 低ランク $\rightarrow$ 圧縮・計算が効率化できる？ ($\rightarrow$ 低ランクではない $\rightarrow$ 表現・基底を変える or 諦める)

---
# 講義パートのまとめ

* Natural tensor representation and quantics reprensentation
* Tensor cross interpolation = adaptive learning algorithm for tensor train
* Various future applications: quantum field theories, ab initio calculaitons, ...
* Open-source implementation
  - C++/Python: $\texttt{xfac}$
  - Julia: $\texttt{TensorCrossInterpolation.jl}$など 最後に少し紹介します！

Future directions: more general tensor networks, parallelization, applications!

(Q)TCIを使って, 低ランクなデータを探してみよう!
本格的に研究に参入したい方は, 最近立ち上げたtensor4allグループに参加可.


---
# $\texttt{xfac}$/ $\texttt{TensorCrossInterpolation.jl}$の紹介

<center>
<span style="font-size: 0.9em; color: red">Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation.</span>
近日publicになるはず.
</center>

$\texttt{xfac}$ ($\texttt{C++}$): こっちがoriginal. 主開発者はY. N. Fernández.

* 一部機能は, $\texttt{Python}$から呼び出せる.

$\texttt{TensorCrossInterpolation.jl}$: 主開発者は, **M. K. Ritter** & H. Shinaoka + 寺崎さんのサポート.

* 複数のJuliaライブラリ ($\texttt{QuanticsTCI.jl}$, $\texttt{QuanticsGrids.jl}$...)と連携
* $\texttt{ITensors.jl}$と連携可 (TCIからITensors.MPS/MPOへの変換など)

---
# 今回のハンズオン


今回は, インストールが簡単なJuliaライブラリ群を使います。

- [Tensors4FieldsのWebサイト](https://gitlab.com/tensors4fields)
- (作成中, 引っ越しするかも) [Tensors4Fieldsのサンプル集](https://tensors4fields.gitlab.io/T4FExamples.jl/dev/index.html)

現在ドキュメント化されているのは, quantisc, TCI部分. ITensors.jlとの連携, 量子フーリエ変換等の演算機能は, 公開されているけど, ドキュメント化されていません ($\texttt{Quantics.jl}$).