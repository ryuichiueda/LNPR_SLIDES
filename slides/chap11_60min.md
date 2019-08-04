## 11. 強化学習

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 強化学習の問題

* 問題はMDPだがエージェントが行動決定する際に状態遷移モデルや報酬モデルが既知として使えない
* エージェントから見えるもの
    * 行動と行動の集合: $a \in \mathcal{A}$
    * 現在時刻までのエピソード: $\\{\boldsymbol{x}_0, a_1, \boldsymbol{x}_1, r_1, a_2, \boldsymbol{x}_2, r_2, \dots, a_t, \boldsymbol{x}_t, r_t \\}$
    * 終端状態に着いたこと, およびそのときに得られる価値$v_\text{f}$
    * 評価: $J(\boldsymbol{x}\_{0:T}, a\_{1:T}) = \sum\_{t=1}^\top r\_t + v\_\text{f}$
* <span style="color:red">経験から方策を得る</span>

---

### 行動価値関数

* ロボットが状態$s$から行動$a$をとるときの価値
    * 状態価値関数$V(s)$に対して行動$a$も変数に加えたもの
    * $Q(s,a)$と表す
    * $V^\*(s) = \max_a Q^\*(s,a)$
* 行動価値関数の値: Q値と呼ぶことがあり、このスライドでもQ値と呼ぶ

---

### 11.1 Q学習

* ロボットが状態$s$から行動$a$で状態$s'$に遷移した直後、状態$s$、行動$a$における行動価値関数の値$Q(s,a)$を次のような式で更新
    * <span style="color:red">$Q(s,a) \longleftarrow (1 - \alpha)Q(s,a) + \alpha \left[ r + \max_{a'} Q(s',a') \right]$</span>
        * $\alpha$: ステップサイズ・パラメータ$(0 \lt \alpha \le 1)$
* 式の意味
    * 次の値の重み付き和
        * $Q(s,a)$: 元のQ値
        * $r + \max_{a'} Q(s',a')$:報酬に遷移後の状態における最大のQ値を足したもの
    * 価値反復に相当する計算を1個の状態遷移だけで行なっている

---

### 基本的なQ学習のアルゴリズム

1. 行動価値関数（Q値のテーブル）を初期化
    * 特になにも事前情報がない場合は一定の値で初期化
        * 価値反復と異なり、終端状態も最初は分からないので特別扱いしない
    * なにか特定の方策に基づいて初期化してもよい
        * サンプルコードはこちら
1. ロボットを行動させながらQ値の更新
    * 行動して報酬を観測したら前ページの式で更新
    * <span style="color:red">どう行動させるか</span>は重要な問題
