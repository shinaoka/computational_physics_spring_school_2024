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

%\newcommand{\ellpminusone}{{\ellp \hspace{-0.2mm}-\hspace{-0.2mm} 1}}
%\newcommand{\ellminusone}{{\ell \hspace{-0.2mm}-\hspace{-0.2mm} 1}}
%\newcommand{\ellplusone}{{\ell  \hspace{-0.2mm}+\hspace{-0.2mm} 1}}
%\newcommand{\ellplustwo}{{\ell  \hspace{-0.2mm}+\hspace{-0.2mm} 2}}
%\newcommand{\ellminustwo}{{\ell \hspace{-0.2mm}-\hspace{-0.2mm} 2}}
%%\renewcommand{\bell}{{\bar \ell}}
%\newcommand{\tell}{{\tilde \ell}}
%\newcommand{\ellp}{{\ell'}}
%\newcommand{\tellp}{{{\tilde \ell}'}}
%\newcommand{\neLL}{{\mathscr{L}}}  %normal size
%% \neLL needs fontsize 7.5 when used in a figure 
%\newcommand{\eLL}{{\mbox{\small$\mathscr{L}$}}}
%\newcommand{\seLL}{{\scriptscriptstyle \! \mathscr{L}}}
%\newcommand{\scripteLL}{{\scriptstyle \mathscr{L}}}
%\newcommand{\sseLL}{{{\mbox{\tiny$\!\mathscr{L}$}}}}
%\newcommand{\boldell}{{\boldsymbol{\ell}}}
%\newcommand{\boldellp}{{\boldsymbol{\ell}}'}
%\newcommand{\eDD}{{\mbox{\small$\mathscr{D}$}}}
%\newcommand{\crit}{{\mathrm c}}
% \newcommand{\cR}{{\mbox{\small$\mathscr{R}$}}}
% \newcommand{\scR}{{\scriptscriptstyle \! \mathscr{R}}}
% \newcommand{\scriptcR}{{\scriptstyle \mathscr{R}}}
% \newcommand{\sscR}{{{\mbox{\tiny$\!\mathscr{R}$}}}}
% \newcommand{\cN}{{\mbox{\small$\mathscr{N}$}}}
% \newcommand{\scN}{{\scriptscriptstyle \! \mathscr{N}}}
% \newcommand{\scriptcN}{{\scriptstyle \mathscr{N}}}
% \newcommand{\sscN}{{{\mbox{\tiny$\!\mathscr{N}$}}}}

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

%\newcommand{\sigmaR}{\sigmas_{\!\scR}}
%\newcommand{\cS}{{{\mbox{\scalefont{0.97}$\mathcal{S}$}}}}
%\newcommand{\scS}{{{\mbox{\scalefont{0.7}$\mathcal{S}$}}}}
%\newcommand{\sscS}{{\scalefont{0.5}\cS}}
%\newcommand{\sigmasS}{\sigmas_{\scS}}
%\newcommand{\localdim}{d}
%\newcommand{\slocaldim}{d}
%\newcommand{\sslocaldim}{d}
% \newcommand{\localdim}{\Delta}
% \newcommand{\slocaldim}{\Delta}
% \newcommand{\sslocaldim}{\Delta}
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
   - Juliaライブラリで実践してみる


---
# 予習資料

- テンソルネットワーク: [昨年の大久保さんの講義の最初30分](https://www.youtube.com/watch?v=i2wsatfsogI)
- Quantics Tensor Train: [日本物理学会誌の記事]()
- Julia予備知識: 初日の講義の寺崎さんの講義で紹介します.

---
# 具体例

xfac論文から、綺麗な具体例をいくつか示す。
流体、場の量子論の図も見せて、モチベートする。

---
# テンソルネットワークとは？ (復習)

![center height:500px](fig/standardTTtoolboxXavierVersion.png)

<center>
<span style="font-size: 0.7em">Taken from Y. N. Fernández, ... , J. von Delft, H. Shinaoka, and X Waintal, in preparation</span>
</center>

---

---


---
# 関数をテンソルネットワーク化する2つの方法

1. Natural representation
1. Quantics representation



---
# Natural representationとは

数ページ使って説明する

---
# Quantics representation

少数変数, 桁違いに違う長さスケールがある場合に有効

---
# そもそも10進法ってなに？

Wikipedia: 「十進法（じっしんほう、（英: decimal system）とは、十を底（てい）とし、底およびその冪を基準にして数を表す方法である。」

---
# 10進法の例

例えば, 3桁の非負整数を考えてみよう ($\mathscr{R}=3$):
$i = 0, 1, 2, \cdots, 10, 11, \cdots, 100, 101, \cdots, 999.$

1つの数字を異なる桁を表す複数の数字の組で表現する.
$i = a_1 \times \textcolor{red}{10^2} + a_2 \times \textcolor{red}{10^1} + a_3 \times \textcolor{red}{10^0} = (a_1 a_2 a_3)_{10}$.

ただし, $a_r \in \{0, 1, \cdots, 9\}$.

---
# なぜ10進法が便利なのか？

桁数$\cR$を増やせば, 表現可能な整数の最大値 ($10^\scR-1$)を指数的に大きく出来る！

$i = a_1 \times \textcolor{red}{10^{\scR-1}} + \cdots + a_r \times \textcolor{red}{10^{\scR-r}} + \cdots + a_\scR \times \textcolor{red}{10^0} = (a_1 a_2 \cdots a_\scR)_{10}$.



---
# 2進法

底10が一般的なのは, 人間の指の数が10本だから.
コンピュータの内部表現は2進数 (0, 1が電気信号のON, OFFと対応).

$i = a_1 \times \textcolor{red}{2^{\scR-1}} + \cdots + a_r \times \textcolor{red}{2^{\scR-r}} + \cdots + a_\scR \times \textcolor{red}{2^0} = (a_1 a_2 \cdots a_\scR)_2$.

ただし, $a_r \in \{0, 1\}$.

<!--
Quanticsでは通常2進数を使うが, 底は任意 (一部の特殊例, フラクタルなどを除く).
-->

---
# Quantics representation (1変数)[1]

関数: $f(x),~x \in [x_\mathrm{min}, x_\mathrm{max}]$を考える。

大きさ$M=2^\scR$の等間隔グリッド:

$$
\{x_\mathrm{min} + \delta \times m: m=0, 1, \cdots, M-1\},~\delta \equiv (x_\mathrm{max} - x_\mathrm{min})/M
$$

関数$f(x)$は, 大きさ$2^\scR$の1次元データ $F_m$に離散化

ここに図

<!--
 $M=2^\cR$ ($m=0, 1, 2, \cdots, M-1$):

$$
m(\bsigma) = m(\sigma_1, \sigma_2, \cdots, \sigma_\scR) = \sum_{r=1}^\scR \sigma_r 2^{\scR -r } = (\sigma_1 \cdots \sigma_\scR)_2.
$$

$\delta = (x_\mathrm{max} - x_\mathrm{min})/M$

$\cR$個の小さな変数$\sigma_r~\in \{0, 1\}$を使う.

\textcolor{red}{ここに図を入れる.}
-->

---
# Quantics representation (1変数)[2]

$m~(=0, 1, \cdots, 2^\scR-1)$を2進数表示:

$$
m = \sum_{r=1}^\scR \sigma_r 2^{\scR -r } = (\sigma_1 \cdots \sigma_\scR)_2.
$$

関数は, $\cR$個の脚を持つテンソルとして見なせる:

$$
F_\bsigma \equiv f(x_\mathrm{min} + \delta \times m)
$$

ここに図

---
# Quantics tensor networks (1変数)


図

トポロジー, 脚の順番には任意性がある!

---
# 演習問題

$f(x) = e^{x}~(x\in [0, 1])$は, ボンド次元1のQTT表現を持つことを示してみましょう.

---
# Quantics representation (2変数):

$f(x, y)$: $x \in [x_\mathrm{min}, x_\mathrm{max}], y\in [y_\mathrm{min}, y_\mathrm{max}]$

等間隔2次元グリッド:

$$
\begin{align}
    m_x &= 0, 1, 2, \cdots, 2^\scR-1~(=M-1) \\
    m_y &= 0, 1, 2, \cdots, 2^\scR-1~(=M-1) \\
\end{align}
$$

2次元グリッドの図

---
# Quantics tensor train (2変数):

Binary coding: $m = (\sigma_1 \cdots \sigma_\scR)_2,~m' = (\sigma'_1 \cdots \sigma'_\scR)_2$

1. Interleaved (交互的な) representation
2. Fused (融合した) representation

多くの物理系では, 同じような長さスケール間のエンタングルメントが強い $\rightarrow$ 近くに配置した方が良い.

---
# 演習問題

$f(x) = e^{x + y}~(x, y\in [0, 1])$の, ボンド次元1のQTT表現を求めてみましょう.

MPO表現の図. テンソルの表現を求めさせる



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
# Example: Quantics + TCI for 1D function


---
# Example: Fourier transform kernel


---
# Tensor Cross Interpolation (TCI) in a nutshell

テンソルの要素を適応的に探索し, 低ランクTT表現を求める. 

* **必要条件**  指定された位置のテンソルの値が計算できる.
* **特徴** テンソル全体の要素を計算する必要はない.
* **デメリット** ヒューリスティクスな手法であり, 一般的な関数に対しては, 成功の保証はない.


---
# Matrix Cross Interpolation (MCI)


---
# TCIのコード例

関数コールだけでOK.

$\cR$

積分できるよ〜

---
# 特異値によるテンソル分解とTCIの比較 (玄人向け)

### 特異値分解
* フロベニウスノルム $\sqrt{\sum_i|x_i|^2}$の意味で最適なランクが決定.
* 全要素読み出しが必要.


### TCI/MCI
* **最大値ノルム** $\max_i |x_i|$の意味で**準最適**なランクを求める.
* 全要素読み出しが不要.


---
# QTTによる演算例

何枚かのスライドを使って, QTTによる演算例を示す.
$f(x, y) = e^{x + y}$の場合を考える.
量子フーリエ変換の例も示す.
