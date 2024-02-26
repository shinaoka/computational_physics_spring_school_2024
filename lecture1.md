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