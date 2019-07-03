## 7. 自己位置推定の諸問題

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### パーティクルの数の可変化

* 信念分布が広くなるほどパーティクルの密度が低くなり近似精度が低下
* $\Rightarrow$<span style="color:red">KLDサンプリング</span>
    * パーティクルの数を可変にできる

---

### KLDサンプリングの考え方

* このような考え方
    * 空間にある幅の格子を想定
    * 信念分布の高密度領域を含む格子にパーティクルが存在すべき
    * 足りないならパーティクルを増やすべき<br />
<img width="50%" src="../figs/kld.png" />
* この処理が必要なのはロボットの移動時
    * 分布が広がるとき
    * 問題
        * パーティクルの数が決まらないと広がった分布を表現できない
        * 広がった分布が決まらないとパーティクルの数が決まらない

---

### パーティクル数の決定 1/3

* とりあえず分布の形状が決まったと仮定してパーティクルの数を求めておきましょう
* 利用する指標: カルバック・ライブラー情報量（KL情報量、KLD）
    * 次の式で二つの分布$P,Q$の「近さ」を比較
        * $D\_\\text{KL}(P||Q) = \\sum_s P(s)\\log \\dfrac{P(s)}{Q(s)} = \\left\\langle \\log P(s) \\right\\rangle\_{P(s)} - \\left\\langle \\log Q(s) \\right\\rangle\_{P(s)}$
        * 分布が一致すると0。そうでないと0より大きくなる

---

### パーティクル数の決定 2/3

* $XY\theta$空間を格子状に分割して離散状態を定義
    * $s_0,s_1,\dots,s_{k-1}$と名前をつける
* $b^\*, b$の各離散状態中の確率を、それぞれ$p^\*_j = P^\*(\boldsymbol{x} \in s_j), \hat{p}_j = \hat{P}(\boldsymbol{x} \in s_j)$とする
    * $p^*_j$: 密度の積分値
    * $\hat{p}_j$: 重みの和
* 二つの分布の比較
    * $D\_\\text{KL}(\\hat{P}||P^\*) = \\sum\_{j=0}^{k-1} \\hat{p}\_j \\log \\dfrac{\\hat{p}\_j}{p^\*\_j }$
    * ただし$P^*$は未知なので計算できない

---

### パーティクル数の決定 3/3

* $D\_\\text{KL}(\\hat{P}||P^\*)$がある値$\varepsilon$以内に収まる確率を考える
    * $P[ D_\text{KL}(\hat{P}||P^*) \le \varepsilon ]$
    * $N$が増えると、この確率は大きくなる<br />（計算はできないけど）
* さらに、この確率を$1-\delta$以上にしたいと仮定し、<br />閾値$\delta$を考える
    * 問題: 必要な$N$は?

---

### 大域的自己位置推定

* 初期姿勢$\boldsymbol{x}_0$を未知とする問題
    * MCLの場合、パーティクルが不足
    * 真の姿勢まわりにパーティクルがないと推定続行不可能

<img width="40%" src="../figs/mcl_global.gif" />

---

### 誘拐ロボット問題

* 誘拐: 人がロボットを運んで別の場所に置く
    * 信念分布の高確率な部分と真の姿勢$\boldsymbol{x}^*_t$が乖離
    * 一度信念を否定する必要があるけどMCLにそのような機能はない

<img width="40%" src="../figs/mcl_kidnap.gif" />

---

### 対策

* 「自己位置推定が間違っている確率」の導入
    * $b(\boldsymbol{x}) = b(\boldsymbol{x} | \Upsilon=0)P(\Upsilon=0) + b(\boldsymbol{x} | \Upsilon=1)P(\Upsilon=1)$
        * $\Upsilon=0$: 正常（自己位置推定が間違っていない） 
        * $\Upsilon=1$: 異常
        * $b(\boldsymbol{x} | \Upsilon=0)$: 自己位置推定で求めた信念
        * <span style="color:red">$b(\boldsymbol{x} | \Upsilon=1)$: 異常だとした場合の信念</span>
* この考えの実装に必要なこと
    * 間違っている確率$P(\Upsilon)$をどうやって求めるか
    * 分布$b(\boldsymbol{x} | \Upsilon=1)$をどう作るか

---

### リセット

* センサ値を信念に反映するときのベイズの定理の分母を使う
    * このときのベイズの定理: $b(\boldsymbol{x}) = \hat{b}(\boldsymbol{x} | \textbf{z}) = \dfrac{ p(\textbf{z} | \boldsymbol{x}) \hat{b}(\boldsymbol{x}) } { p(\textbf{z}) } = \dfrac{ p(\textbf{z} | \boldsymbol{x}) \hat{b}(\boldsymbol{x}) } { \langle p(\textbf{z} | \boldsymbol{x}') \rangle_{\hat{b}(\boldsymbol{x}')}}$
    * $\alpha = \langle p(\textbf{z} | \boldsymbol{x}') \rangle_{\hat{b}(\boldsymbol{x}')}$とする
* $\alpha$は信念$b$に対して$\textbf{z}$がどれくらいあり得るかを数値化したもの（周辺尤度）
    * とりあえずシンプルに考えて、$\alpha$が閾値を下回ったら$P(\Upsilon=1)=1$（異常）としましょう

---

### 異常時の信念分布の構成

* 考え方は次の3つ
    * 一様分布と考える
        * 単純リセット 
    * センサ値が正しいと考える
        * センサ値にしたがってパーティクルを置き直し$\rightarrow$センサリセット
    * センサ値は信用できないけど信念も少し不確かになる
        * 信念分布をなだらかにする$\rightarrow$膨張リセット

---

### センサリセット

* 得られたセンサ値が得られそうな姿勢にパーティクルを置き直し
    * $p(\boldsymbol{x} | \boldsymbol{z} )$からドロー
* センサ値が間違っていたら今までの推定が無駄に
    * 起こる条件を厳しくするか、今までのパーティクルを残す

<img width="40%" src="../figs/sensor_reset.gif" />

---

### 膨張リセット

* 今までの分布を膨張させる
* 分布が大きく変化しないので頻繁に起こせる
* 収束まで時間がかかる

<img width="40%" src="../figs/expansion_reset.gif" />

---

### 組み合わせ

* 通常は膨張リセット
* 何回か連続で$\alpha$が閾値を下回ったらセンサリセット

<img width="40%" src="../figs/expansion_sensor_reset.gif" />
