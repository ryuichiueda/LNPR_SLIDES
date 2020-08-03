$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 12. 部分観測マルコフ決定過程<br />（前半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 12.1 POMDP

* POMDP
    * MDPで、エージェントが状態推定をしなければならない問題<br />　
* 名前の意味
    * partially observable Markov decision process
    * 部分観測マルコフ決定過程

---

## 12.1.1 POMDPの問題

* エージェントに与えられるもの
    * 状態遷移モデル: $p(\V{x}' | \V{x}, a)$
    * <span style="color:red">観測モデル: $p(\V{z} | \V{x})$</span>
    * 報酬モデル: $r(\V{x}, a, \V{x}')$
    * 終端状態の価値: $V_\text{f}(\V{x}_T)$<br />　
* エージェントの目的
    * $J(\V{x}\_0, a\_{1:T}) = \sum\_{i=1}^T r(\V{x}\_{t-1}, a\_t, \V{x}\_t) + V(\V{x}\_T)$の最大化

MDPと異なり、$\V{x}$が分からない

---

### POMDPの方策

* $\V{x}$が分からないので入力に使えない
    * $\Pi(\V{x})$という形式にはならない<br />　
* 次のようなものになる
    * $a\_{t+1} = \Pi\_\text{POMDP}(a\_{1:t}, \textbf{z}\_{1:t}, r\_{1:t})$
        * 「これまでやったこと、見たこと、与えられた報酬から行動を決めてください」
        * 価値反復では求められなさそう

非常に難しいが、自己位置推定と<br />併用すると、少し簡単になる

---

## 12.1.2 状態推定が不確かな場合に起こる問題

* 自己位置推定の結果を使うことを考える
    * 信念分布から代表値$\hat{\V{x}}_t$を選ぶ
    * 価値反復などで得られた方策$\Pi$から$\Pi(\hat{\V{x}}_t)$で行動を選べる<br />　

問題: 信念分布$b_t$の形状や広さが<br />行動決定に反映されない


---

### $b_t$の形状や広さを考慮しない行動決定で起こること

* 最尤なパーティクルを$\hat{\V{x}}_t$とした例
    * $\hat{\V{x}}_t$と真の姿勢がずれると水たまりに引きずり込まれる

<img width="65%" src="./figs/12.1.jpg" />
