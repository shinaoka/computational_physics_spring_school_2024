---
marp: true
header: Tensor representation learning for physics
footer: ©2024 H. Shinaoka
# Page number
paginate: true
---

<!-- To show total number of pages for paginate = true -->
<style>
section::after {
  content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);
}
</style>

<!-- add center keyword for figure -->
<style>
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
$$

Tensor representation learning for physics (1)
===

##### 品岡寛 (埼玉大学)

![center height:280px](fig/IR.png)

---
# 自己紹介 [https://shinaoka.github.io](https://shinaoka.github.io)

* 経歴
  - 博士 (工学) 2009年3月@東大物工
  - ポスドク 2009年3月〜2015年9月 (東大、産総研、チューリッヒ連邦工科大)
  - 埼玉大 2015年10月〜
* 専門
  - 量子多体理論、第一原理計算、幾何学的フラストレート磁性･･･
  - 情報縮約技術と場の量子論を融合することに興味 ($\rightarrow$ 計算物性物理のフロンティアを拡げたい！)

---
# 今日のお話の概要

1. 関数 $\rightarrow$ テンソルネットワーク？
   - Tensor network
   - Quantics representation
   - Tensor Cross Interpolation (TCI)
2. Juliaライブラリで実践してみる

英語と日本語が混じっているのは気にしないで下さい...

---
# 具体例

* 2Dの例


---
# テンソルネットワークとは？ (復習)

ここでいうテンソルとは、単なる多次元データです (難しい物理・数学の話は忘れてください)。


---
# Quantics representation

---
# そもそも10進法ってなに？

Wikipedia: 「十進法（じっしんほう、（英: decimal system）とは、十を底（てい）とし、底およびその冪を基準にして数を表す方法である。」

---
# 10進法の例

例えば, 3桁の非負整数を考えてみよう ($\mathscr{R}=3$)。
$i = 0, 1, 2, \cdots, 10, 11, \cdots, 100, 101, \cdots, 999$

1つの数字を異なる桁を表す複数の数字の組で表現する。
$i = a_1 \times 10^2 + a_2 \times 10^1 + a_3 \times 10^0 = (a_1 a_2 a_3)_{10}$.

ただし, $a_r = 0, 1, \cdots, 9$.

---
# なぜ10進法が便利なのか？

桁数$\mathscr{R}$を増やせば, 整数の最大値 ($10^\mathscr{R}-1$)を指数的に大きく出来る！

$i = a_1 \times 10^2 + a_2 \times 10^1 + a_3 \times 10^0 = (a_1 a_2 a_3)_{10}$.



---
# 2進法

底を10と普通選ぶのは, 人間の指の数が10本だから。現代のコンピュータでは2進数が内部表現として用いられる (0, 1が電気信号のON, OFFと対応).

$i = a_1 \times 2^{\mathscr{R}-1} + \cdots + a_r \times 2^{\mathscr{R}-r} + \cdots + a_\mathscr{R} \times 2^0 = (a_1 a_2 \cdots a_\mathscr{R})_2$.

Quanticsでは通常2進数を使うが, 底は任意.