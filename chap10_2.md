$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 10. マルコフ決定過程と動的計画法<br />（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 10.4 価値反復

* これまでやってきたこと
    * 水たまりに突っ込んでいく方策の状態価値関数を求めた<br />　
* 方策はもっとよくできる
    * 方策を局所的に変えると状態価値関数の値を改善可能

---

### 方策改善

* <span style="color:red">行動価値関数</span>
    * 行動を変数にして価値を考える
    * $Q^\Pi(s, a)  = \Big\langle R(s, a, s') + V^\Pi(s') \Big\rangle_{ P(s' | s, a) }  \\\\ \qquad = \sum_{s'} P(s' | s, a) \left[ R(s, a, s') + V^\Pi(s') \right]$<br />　
* 行動価値関数を利用した方策の改善
    * $\Pi(s) \longleftarrow \text{argmax}_{a \in \mathcal{A}} Q^\Pi(s, a)$
    * 価値の計算（方策評価）と、この計算（方策改善）を繰り返すと方策がよくなっていきそうだ

これを突き詰めると次ページのアルゴリズムに

---

### 価値反復

* 次のようなアルゴリズムを考える
    1. 全状態について適当に状態価値関数$V$を初期化
        * ただし終端状態の価値だけは決まった値に
    2. 各状態において, 全行動$a \in \mathcal{A}$の行動価値関数を求め, 最大の値を$V(s)$に代入
        * $V(s) \longleftarrow \max_{a \in \mathcal{A}} Q(s, a)$
    3. 2を何スイープも実行
    4. 収束後、各状態で行動価値関数を最大にする行動を記録
        * $\Pi^\*(s) = \text{argmax}\_{a \in \mathcal{A}} \sum\_{s'} P(s' | s, a) \left[ R(s, a, s') + V^\*(s') \right]$
            * $V^*$: 収束した状態価値関数（<span style="color:red">最適状態価値関数</span>）
            * $\Pi^*$: <span style="color:red">最適方策</span>

