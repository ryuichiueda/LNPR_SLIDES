$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 12. 部分観測マルコフ決定過程<br />（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 12.4 AMDP

* 能動的に観測を考慮した行動を生成したい
    * 例
        * ランドマークが観測できるルートをとる
        * 10歩先の信念分布の広がりにあわせて障害物から距離をとる
    * Q-MDPでは無理<br />　
* <span style="color:red">信念状態を状態とみなして価値反復</span>
    * 状態空間を信念分布を表すパラメータで拡張
        * augmented MDP、AMDP
    * 信念分布に価値がつく$\Longrightarrow$状態が確かなほど価値が高く
    * 信念分布が遷移して不確かになっていくことを予測して行動可能

---

## 12.4.1 信念状態空間の離散化

* 状態空間を$XY\theta$空間から$XY\theta\sigma$空間に拡張
    * $\sigma = |\Sigma_t|^{1/6}$: 信念分布の広さ
        * $XY\theta$の3軸の標準偏差の平均値と考えるとよい
            * [rad]と[m]が入り混じっているので適当
        * 計算方法は書籍に（分布のエントロピーに基づき計算）
        * どんな形状の分布も$\sigma$ひとつで表現されるため、形状の分布に応じた行動決定は不可能（計算量とのトレードオフ）<br />　
* $\sigma$を5段階に離散化
    * 10, 11章は$XY\theta$空間を$57,600$個に離散化していたが、これが$5$倍の$288,000$個に（価値反復の計算量も増加）

---

## 12.4.2 移動による不確かさの<br />遷移の計算

* 状態遷移モデルを拡張しなければならない
    * $\V{x}\rightarrow\V{x}'$ではなく、$\V{b}\rightarrow\V{b}'$に
    * $\textbf{z}$も計算の際に考慮
        * $\textbf{z}$: 複数のランドマークを観測して得られるセンサ値のリスト<br />　
* カルマンフィルタによる自己位置推定を想定して計算してみましょう
    * 移動による状態遷移と観測による状態遷移を別々に計算して、あとから組み合わせ

---

### 移動による信念分布の遷移

* $XY\theta$空間での遷移
    * $\hat{\V{\mu}}\_t = \V{f}(\V{\mu}\_{t-1}, a\_t)$
        * カルマンフィルタで計算済み
        * 分布の中心は状態遷移関数の通りに動く
* $\sigma$の遷移
    * $\hat{\sigma}\_t = |\sigma\_{t-1}^2F\_t F\_t^\top + A\_{t-1}M\_t A\_{t-1}^\top|^{1/6}$
        * カルマンフィルタで計算した$\hat{\Sigma}\_t = F\_t\Sigma\_{t-1}F\_t^\top + A\_{t-1}M\_t A\_{t-1}^\top$に$\Sigma\_{t-1} = \sigma^2\_{t-1}I$を代入
            * 移動前の共分散行列を動きのヤコビ行列で移した共分散行列と、動きの不確かさによる共分散行列を足すと移動後の共分散行列に<br />　

このふたつの式から離散状態での状態遷移を算出

---

### 移動による離散状態の遷移

* 手順（10章の価値反復とほぼ同じ）
    1. 離散状態の$XY\theta\sigma$から状態を等間隔にサンプリング
    2. 前ページの式で遷移させ、遷移後の$\V{x}'$が存在する離散状態について$\V{x}'$を数えて遷移確率を算出<br />　
* 終端状態を選ぶ
    * $XY\theta$空間でゴールに収まり、$\sigma$が最小の区分のものを終端状態に

---

### 価値反復の実行

* この時点で価値反復は実行可能
* しかし$\sigma$が悪くなる一方なので収束はしない
    * 観測が反映されていない

<img width="80%" src="./figs/12.7.jpg" />
