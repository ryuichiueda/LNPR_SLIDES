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
        * 実装の説明のときに説明します

---

### 実装の準備

* [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_reinforcement_learning/q1.ipynb)
    * `QAgent`クラスに、とりあえずゴールにまっすぐ向かう方策（PuddleIgnoreAgent）を実装しておく（`policy`メソッド）<br />
<img width="35%" src="../figs/q_puddle_ignore.png" />
* PuddleIgnoreAgentの方策評価`policy_evaluation.ipynb`の結果をファイルに書き出し

---

### 状態価値関数の実装と初期化

* [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_reinforcement_learning/q2.ipynb)
    * `StateInfo`クラス: 各状態のQ値やQ値から得られる行動を管理
        * `greedy`メソッド: Q値が最大の行動を返す
    * `QAgent`クラスの`set_action_value_function`メソッドで初期化
        * PuddleIgnoreAgentの方策評価`policy_evaluation.ipynb`の結果を流用
            * この方策をQ学習で改善していく
        * Q値の初期値の与え方
            * `PuddleIgnoreAgent`と一致する行動に対するQ値: 方策評価で得られた状態価値
            * 一致しない行動に対するQ値: 方策評価で得られた状態価値を少し下げる
    * これで、Q値経由で`PuddleIgnoreAgent`の方策が再現される

---

### 現段階での方策の問題

* Q値から導かれる方策以外をエージェントが試さない
    * 現状の方策で得られる行動以外のQ値が変更されない
    * もっと良い方策を探せない
* <span style="color:red">たまに現状のQ値で良いとされない行動を選ぶ</span>ことが必要
    * 選びすぎるとゴールに行かなかったりあまりにも無駄が多くなったりするので適度に
    * Q学習の場合はどんな行動をとってもQ値の計算に悪影響がない
        * なぜでしょうか

---

### 探索的な方策

* 本の実装例では「$\varepsilon$-グリーディ方策」を実装
    * Q値の示す最適な行動以外の行動を、ある決まった確率でランダムに選択
        * 最も単純
* 他にボルツマン方策など
* $\varepsilon$-グリーディ方策の実装
    * [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_reinforcement_learning/q3.ipynb)
