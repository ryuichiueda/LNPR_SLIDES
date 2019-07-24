## 10. マルコフ決定過程と<br />動的計画法

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 1. 移動ロボットの行動決定

* 移動だけならば探索手法を利用して経路を算出
* ロボットにはもう少し難しい判断も必要
    * 経路の危険性などの判断
    * 仕事をするかしないか

$\Rightarrow$ 一般的な枠組みで移動を考えてみる

---

### マルコフ決定過程

* システム（ロボット）の行動を次のようにモデル化
    * ロボットは状態遷移する
        * $\boldsymbol{x}\_t \\sim p(\boldsymbol{x} | \boldsymbol{x}\_{t-1}, a\_t)  \\qquad (t=1,2,\\dots,T)$
            * $T$: タスクが終わる時間（不定）
            * $\boldsymbol{x}_T$: 終端状態
            * $a \in \mathcal{A}$: 行動（制御出力を記号化したもの）
        * ロボットは真の状態を知っている
    * ロボットの行動は評価される（次のページで詳しく）
        * $J(\boldsymbol{x}\_{0:T}, a\_{1:T}) \in \Re$

---

### 行動の評価

* どう動いて、結果どうなったかを数値化
    * $J(\boldsymbol{x}\_{0:T}, a\_{1:T}) = \sum\_{t=1}^\top r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V\_\text{f}(\boldsymbol{x}\_T)$
        * $r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) \in \Re$: 状態遷移ごとに与える評価を決める関数
            * 報酬モデルと呼ぶ
            * 値を報酬と呼ぶ
        * $V\_\text{f}(\boldsymbol{x}\_T)$: 終端状態の評価

---

### 評価の具体例

* 最短時間で移動したい
    * $\rightarrow r$を$\boldsymbol{x}\_{t-1}$から$\boldsymbol{x}\_{t}$への経過時間$\times (-1)$に
* 低燃費で移動したい
    * $\rightarrow r$を$\boldsymbol{x}\_{t-1}$から$\boldsymbol{x}\_{t}$までの燃費$\times (-1)$に
* きれいに車庫入れしたい
    * $V\_\text{f}(\boldsymbol{x}\_T)$の値を終端状態$\boldsymbol{x}\_T$で変える

実際はこれらのような要求が複合的に生じるが、最終的に1つの値で評価

---

### 方策と状態価値関数（1/2）

* 「決まったルールでエージェントが行動決定するとき、ある初期姿勢$\boldsymbol{x}_0$からロボットが行動したらどんな評価になるか」という問題を考える
* 決まったルールとして、とりあえず次のような<span style="color:red">方策</span>を考える
    * $\Pi: \mathcal{X} \rightarrow \mathcal{A}$
        * ある状態のときに決まった行動を返す関数
* $\Pi$で行動した時の評価の期待値を<span style="color:red">$V(\boldsymbol{x}_0)$</span>とする

---

### 方策と状態価値関数（2/2）

* 方策が決まると、試行によってある初期状態からのエピソードが複数得られる
    * $\\{\boldsymbol{x}_0, a_1, \boldsymbol{x}_1, r_1, a_2, \boldsymbol{x}_2, r_2, \dots, a_T, \boldsymbol{x}_T, r_T \\}$
    * あるエピソードが生成される確率: $p(\boldsymbol{x}\_{1:T}|\boldsymbol{x}\_0, \Pi)$
        * 行動、報酬は$\boldsymbol{x}$から決定される
* エピソードの評価値の平均値が$V(\boldsymbol{x}_0)$
    * $V^\Pi(\boldsymbol{x}\_0) = \left\langle \sum\_{t=1}^\top r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V(\boldsymbol{x}\_T) \right\rangle\_{p(\boldsymbol{x}\_{1:T}|\boldsymbol{x}\_0, \Pi)}$

---

### 価値関数の性質（1/2）

* 漸化式にできる
    * $V^\Pi(\boldsymbol{x}\_0) = \left\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) + \sum\_{t =2}^T r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V(\boldsymbol{x}\_T)  \right\rangle\_{p(\boldsymbol{x}\_{1:T}|\boldsymbol{x}\_0, \Pi)} \\\\ \hspace{-2em}= \Big\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) \Big\rangle\_{p(\boldsymbol{x}\_{1:T}|\boldsymbol{x}\_0, \Pi)} + \left\langle \sum\_{t =2}^T r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V(\boldsymbol{x}\_T)  \right\rangle\_{p(\boldsymbol{x}\_{1:T}|\boldsymbol{x}\_0, \Pi)} \\\\ \hspace{-2em}= \Big\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) \Big\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, \Pi )} + \left\langle \sum\_{t =2}^T r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V(\boldsymbol{x}\_T)  \right\rangle\_{p(\boldsymbol{x}\_{2:T}|\boldsymbol{x}\_1, \boldsymbol{x}\_0, \Pi)p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, \Pi)} \\\\ \hspace{-2em}= \Big\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) \Big\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, a\_1 )} + \left\langle \left\langle \sum\_{t =2}^T r(\boldsymbol{x}\_{t-1}, a\_t, \boldsymbol{x}\_t) + V(\boldsymbol{x}\_T)  \right\rangle\_{p(\boldsymbol{x}\_{2:T}|\boldsymbol{x}\_1, \Pi)} \right\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, a\_1)} \\\\ \hspace{-2em}= \Big\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) \Big\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, a\_1 )} + \left\langle V^\Pi(\boldsymbol{x}\_1) \right\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, a\_1)} \\\\ \hspace{-2em}= \Big\langle r(\boldsymbol{x}\_0, a\_1, \boldsymbol{x}\_1) + V^\Pi(\boldsymbol{x}\_1) \Big\rangle\_{p(\boldsymbol{x}\_1 | \boldsymbol{x}\_0, a\_1)}$

---

### 価値関数の性質（2/2）

* 漸化式にすると状態が初期状態かどうかは関係なく価値$V(\boldsymbol{x})$が決まることが分かる
    * <span style="color:red">$V^\Pi(\boldsymbol{x}) = \left\langle r(\boldsymbol{x}, a, \boldsymbol{x}') + V^\Pi(\boldsymbol{x}') \right\rangle_{p(\boldsymbol{x}'| \boldsymbol{x}, a)}$</span>
* 遷移後の状態の価値が分かるなら次のことが成り立つ
    * 遷移前の状態の価値も分かる
    * $a$を変えて価値が高くなるなら$\Pi$を変更すればよい
    * 現在の状態での行動を考えるときに次の状態でどんな行動をとるか考えなくてよい

---

### 10.2. 経路計画問題

* 右図の環境での移動を考える
    * [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_mdp/puddle_world3.ipynb)
    * ゴールが一つ
    * 水たまり
* 行動
    * 前進、右回転、左回転の3種類
* 報酬を(次の項目の和)$\times$(-1)で
    * 水たまりに入った時間$\times$深さ$\times$係数
    * ゴールまでの移動時間
* 右の行動例（[コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_mdp/puddle_world4.ipynb)）:
    * 水たまり無視でゴールへ直行
    * 評価を高めるように改善したい

<img width="40%" src="../figs/puddle_world4.gif" />


---

### 10.3. 方策の評価


