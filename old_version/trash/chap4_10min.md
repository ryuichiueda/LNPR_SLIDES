## 4. 不確かさのモデル化

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 本章の内容

* 移動の不確かさ
* 観測の不確かさ

---

### ロボットの移動に関する不確かさ

* 不確かになる（センサがないとロボットがどこにいるか分からなくなる）様々な原因
    * A. 一瞬
        * 小石への片輪の乗り上げ, 走り出し/停止時のロボットの揺れなど
    * B. 数秒から数十秒継続
        * 縁石への乗り上げ, 走行環境の傾斜
    * C. 走行中ずっと継続
        * 左右の車輪にかかる荷重のバランスやモータの個体差, タイヤの状態
    * D. 雑音のレベルを超えたもの
        * 走行不能になるレベルの障害物へのスタック, 人間の干渉

---

### 実装する不確かさの原因

* 雑音とバイアス
    * 雑音: 離散時刻ごとに制御指令に乱数を混ぜる（偶然誤差）
    * バイアス: 制御指令を一定量ずらす（系統誤差）
        * ずらす量は最初に決める
    * この二つで様々な継続期間の誤差要因を代表
* スタックと誘拐
    * スタック: 突然ロボットがある期間動かなくなる
    * 誘拐: 突然ロボットの姿勢が別の姿勢にワープ
    * 「雑音のレベルを超えたもの」の動かないものと動くもの

---

### 雑音の実装

* 雑音のモデル
    * 小石を踏んで向き$\theta$が少し変わる
    * 小石を踏む頻度は移動量（道のり）に比例
        * 小石を踏んだら次に踏むまでの道のりを$x \sim p(x)$というようにドローするという実装になる
        * ここでの$x$は道のり
        * $p(x)$はどんな分布になるか？

---

### 指数分布

* $p(x | \lambda ) = \lambda e^{-\lambda x}  \qquad (x \ge 0)$
    * $x$: 時間や距離など
    * $\lambda$: $x$あたりに何かが起こる回数（期待値）
    * [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_uncertainty/exponential.ipynb)

![](../figs/exponential.png)
<span style="font-size:50%">図: $\lambda = 1/5$の指数分布</span>

---

### 実装

* [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_uncertainty/noise_simulation2.ipynb)
    * 1[m]あたり小石5個
    * 小石を踏んだら$\Delta\theta \sim \mathcal{N}(0, 3\text{[deg]})$だけ$\theta$をずらす

![](../figs/motion_noise.gif)

---

### バイアスの実装

* 速度$\nu$、角速度$\omega$にそれぞれ一定の誤差$\Delta\nu, \Delta\omega$を足す
    * 乱数で初期値$\Delta\nu, \Delta\omega$の値を決めてずっとその値を使う
    * [コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_uncertainty/noise_simulation3.ipynb)のデフォルト値: $\Delta \nu \sim \mathcal{N}(0, 0.1^2)$、$\Delta \omega \sim \mathcal{N}(0, 0.1^2)$

![](../figs/motion_bias.gif)

---

### スタック


