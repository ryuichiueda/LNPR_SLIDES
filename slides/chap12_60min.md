## 12. 部分観測マルコフ決定過程

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 12.1 部分観測マルコフ決定過程（POMDP）

---

### 自己位置推定が不確かな場合に<br />起こる問題

* 分布の広さを考慮せずに一つの推定姿勢で行動決定
    * 分布の端にロボットがいる可能性が考慮されない
* 人間なら状況が不確かならどうするだろうか？
    * 調べる、警戒する、迂闊に危険に近づかない

<img src="../figs/uncertain_navigation_1.png" />

---

### 別の例

* 外側から見ていると非常に不可解

<img src="../figs/uncertain_navigation_2.png" />

---

### POMDP

* 問題はMDPだがエージェントが真の状態を把握できない
    * 自己位置推定などで自身で観測しなければならない
    * つまり今の例のような問題を扱うための枠組み
* POMDPにおける方策: $a\_{t+1} = \Pi\_\text{POMDP}(a\_{1:t}, \textbf{z}\_{1:t}, r\_{1:t})$
    * 状態がわからないのでこれまでに知ったことから行動を判断
    * この形式のままだと実装方法は見えない


---

### <span style="text-transform:none">belief MDP</span>

* 分布自体を「状態」と考える
    * <span style="color:red">信念状態</span>
* 信念状態を使った方策
    * $a\_{t+1} = \Pi\_\text{b}(b_t)$
    * 全種類の分布に対して行動を返す
    * 分布の状態遷移が分かれば理論の上では価値反復で計算可能
    * 自己位置推定するなら素直なPOMDPへのアプローチ
    * 分布の種類は膨大


---

### <span style="text-transform:none">belief MDP</span>

* 本書では2種類紹介
    * Q-MDP系: MDPでの解を使い回す
    * AMDP系: なんらかの近似を用いて信念状態で価値反復

---

### Q-MDP

* MDPでの最適行動価値関数$Q^*$と信念分布$b$から、次の期待値を基準に行動決定
    * $Q_\text{MDP}(a,b) = \Big\langle Q(a, \boldsymbol{x}) \Big\rangle_{b(\boldsymbol{x})}$
    * $\Pi\_{Q_\text{MDP}}(b) = \arg\max_a Q_\text{MDP}(a,b)$
