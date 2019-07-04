## 7. 自己位置推定の諸問題

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 7.1 KLDサンプリング

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

### パーティクル数の決定 1/7

* とりあえず分布の形状が決まったと仮定してパーティクルの数を求めておきましょう
* 利用する指標: カルバック・ライブラー情報量（KL情報量、KLD）
    * 次の式で二つの分布$P,Q$の「近さ」を比較
        * $D\_\\text{KL}(P||Q) = \\sum_s P(s)\\log \\dfrac{P(s)}{Q(s)} = \\left\\langle \\log P(s) \\right\\rangle\_{P(s)} - \\left\\langle \\log Q(s) \\right\\rangle\_{P(s)}$
        * 分布が一致すると0。そうでないと0より大きくなる

---

### パーティクル数の決定 2/7

* $XY\theta$空間を格子状に分割して離散状態を定義
    * $s_0,s_1,\dots,s_{k-1}$と名前をつける
    * 統計学的には「ビン」と呼ばれる
    * $k$: ビンの数
* $b^\*, b$の各ビンに対応する確率を、それぞれ$p^\*_j = P^\*(\boldsymbol{x} \in s_j), \hat{p}_j = \hat{P}(\boldsymbol{x} \in s_j)$とする
    * $p^*_j$: 密度の積分値
    * $\hat{p}_j$: 重みの和
* 二つの分布の比較
    * $D\_\\text{KL}(\\hat{P}||P^\*) = \\sum\_{j=0}^{k-1} \\hat{p}\_j \\log \\dfrac{\\hat{p}\_j}{p^\*\_j }$
    * ただし$P^*$は未知なので計算できない

---

### パーティクル数の決定 3/7

* $D\_\\text{KL}(\\hat{P}||P^\*)$がある値$\varepsilon$以内に収まる確率を考える
    * $P[ D_\text{KL}(\hat{P}||P^*) \le \varepsilon ]$
    * $N$が増えると、この確率は大きくなる<br />（計算はできないけど）
* さらに、この確率を$1-\delta$以上にしたいと仮定し、<br />閾値$\delta$を考える
    * 問題: 必要な$N$は?

---

### パーティクル数の決定 4/7

* 今度は「$N$個のパーティクルを$P^*$にしたがってドローしたときに、各ビンにそれぞれ$n_0, n_1, n_2,\dots, n_{k-1}$個入る確率」を考える
    * この確率は次のような多項分布に従う
        * $P^\*\_\\text{M}(n\_0, n\_1, \\dots, n\_{k-1} | p^\*\_0, p^\*\_1, \\dots, p^\*\_{k-1}) = \\dfrac{N!}{\\prod\_{j=0}^{k-1} (n\_j !) }\\prod\_{j=0}^{k-1} (p^\*\_j)^{n\_j}$
        * 記号をまとめて$P^\*\_\\text{M}(\textbf{n} | \Theta^*)$と表現
* さらに、実際に$N$個パーティクルを選んで$\hat{p}_j$が確定したとして$P^\*\_\\text{M}(\textbf{n} | \Theta^*)$を近似
    * $\hat{P}\_\\text{M}(\textbf{n} | \hat{\Theta})$と表現
        * $\hat\Theta = \{\hat{p}_0, \hat{p}_1, \dots, \hat{p}\_\{k-1\}\}$
    * 解釈が難しいが、$\textbf{n}$はあくまで未定の変数で、$\hat\Theta$は$N$個サンプリングが済んだときに確定するパラメータ

---

### パーティクル数の決定 5/7

* $P^\*\_\\text{M}(\textbf{n} | \Theta^*)$と$\hat{P}\_\\text{M}(\textbf{n} | \hat{\Theta})$の対数尤度比$\log \lambda_N$を計算
    * $\\log \\lambda\_N = \\log \\dfrac{L(\\hat\\Theta | \\textbf{n})}{L(\\Theta^\* | \\textbf{n})} = \\log \\dfrac{ \\prod\_{j=0}^{k-1} \\hat{p}\_j^{n\_j} } { \\prod\_{j=0}^{k-1} {p^\*\_j}^{n\_j}} \\\\ =  \\log \\prod\_{j=0}^{k-1} ( \\hat{p}\_j / p^\*\_j )^{n\_j} = \\sum\_{j=0}^{k-1} \\log ( \\hat{p}\_j / p^\*\_j )^{n\_j} = \\sum\_{j=0}^{k-1} n\_j \\log ( \\hat{p}\_j / p^\*\_j )$
* この対数尤度比は$\hat{P}\_\\text{M}(\textbf{n} | \hat{\Theta})$が最大となる$n_j = \hat{p}_j N$のときに最大になる。このとき
    * $\\log \\lambda\_N = \\sum\_{j=0}^{k-1} \\hat{p}\_j N \\log ( \\hat{p}\_j / p^\*\_j ) = N \\sum\_{j=0}^{k-1} \\hat{p}\_j \\log ( \\hat{p}\_j / p^\*\_j ) \\\\ = N D\_\\text{KL}(\\hat{P}||P^\*)$

---

### パーティクル数の決定 6/7

* 次の性質を使って$N$を求める問題となる
    * $P[ D_\text{KL}(\hat{P}||P^*) \le \varepsilon ] \ge 1 - \delta \Longrightarrow$
$P[ 2\\log \\lambda\_N \le 2N \varepsilon ] \ge 1 - \delta$
    * $2\\log \\lambda\_N \sim \chi^2\_{k-1}$
* $\chi^2_k$: 自由度$k$の<span style="color:red">カイ二乗分布</span>
    * $\chi^2_k (x) = \dfrac{1}{2^{k/2} \Gamma(k/2) } x^{k/2-1} e^{-x/2}$
        * $\Gamma(\alpha) = \int_0^\infty t^{\alpha-1}e^{-t} dt$（ガンマ関数）

---

### パーティクル数の決定 7/7

* 次を満たす$y = 2\log \lambda_N$を求める
    * $\int_0^y \chi^2_{k-1}(x) dx = 1 - \delta$
* $2N\varepsilon > y$を満たす最小の$N$が必要なパーティクル数
    * $N > \dfrac{y}{2\varepsilon}$
* 下図: 信念分布が高い部分にかかるビンの数と<br />必要なパーティクル数
    * $\varepsilon = 0.1, \delta = 0.01$

<img width="60%" src="../figs/kldtest.png" />

---

### サンプリングのアルゴリズム

1. 元のパーティクルの分布から一つずつパーティクルをドローして$p(\boldsymbol{x}'|\boldsymbol{x},\boldsymbol{u}_t)$で移動
1. パーティクルの入っているビンの数を数える
    * パーティクルを移動するごとに新しいビンに入ったらカウンターを増加
1. ビンの数に対応する必要なパーティクルの数を算出
1. ドローした数が3の数を上回ったら終了

---

### [実装](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_advanced_localization/kld_mcl.ipynb)

![](../figs/kld.gif)

---

### 7.2 より難しい自己位置推定

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

### 7.3 推定の誤りの考慮

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

---

### 7.4 MCLにおける変則的な分布の利用

* ガウス分布で表現できない不確かさの例
    * レーザレンジファインダーなどで壁までの距離を計測しているときに人がよく遮る
    * 室温や湿度、外光でセンサの特性が変わる（そしてそのルールがよくわからない）
        * 短期的にはガウス分布でも分布自体が動く
* カルマンフィルタだと取り扱いが難しいがMCLだと尤度関数を作れば簡単

---

### [シミュレータに実装されている例](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_uncertainty/noise_simulation11.ipynb)

* カメラで点ランドマークの大きさを計測するときのオクルージョン
    * 何かが遮って実際よりも小さく見える = 遠く見える

![](../figs/occlusion.gif)

---

### 尤度関数の設計

* オクルージョンの場合は片側を一様分布状にすることが多い
    * 下図: シミュレーションのモデルに対する尤度関数
        * 計測距離$\ell$が実際の距離$\ell^*$より遠くなることがあるので、そちらの尤度を大きく見積もる<br />
![](../figs/phantom_likelihood.png)
    * LiDARの場合は逆にセンサ値が実際より近くなるので反対側が一様分布になる

---

### 非ガウス分布状の尤度関数の効果

* 左: オクルージョンを考慮していない尤度関数
    * 中心からパーティクルが動かない
        * ランドマークから遠いところの尤度が高くなりがちなので
* 右: オクルージョンを考慮した尤度関数（[実装](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_advanced_localization/occlusion_free_mcl.ipynb)）
    * 異常なセンサ値は基本的に無視される
        * どのパーティクルの尤度も一様分布状のところで評価されるので同じ

<img width="35%" src="../figs/occlusion_mcl.gif" />
<img width="35%" src="../figs/occlusion_free_mcl.gif" />
