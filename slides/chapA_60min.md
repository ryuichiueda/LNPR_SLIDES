## A. ベイズ推論

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### A.1 共役事前分布と<br />ベイズの定理を使った推論

* やること: 2章で出てきた下の分布にガウス分布をあてはめる
    * [データ](https://raw.githubusercontent.com/ryuichiueda/LNPR_BOOK_CODES/master/section_sensor/sensor_data_200.txt)
    * 2章では統計値から平均値と分散を計算して求めた
    * 本章ではベイズ的なアプローチをとる

<img width="30%" src="../figs/sensor_200_histgram.png" />

---

### ガウス分布のパラメータ推定

* 次のような状況を考える
    * これから2章と同じ方法で200[mm]手前から壁の距離をLiDARで計測
    * おそらくセンサ値は200[mm]付近でばらつくと考えられる
    * ばらつきはガウス分布だろうと考えられる<br />$ $
* 最初に考えていたガウス分布をセンサ値が入るごとに修正したい。あるいは一度に多数のセンサ値を反映させて修正したい

---

### 「最初に考えていたガウス分布」

* 例えば次のように表す
    * $p(z | \mu, \lambda) = \mathcal{N}(z | \mu, \lambda^{-1})$
        * $\lambda$は精度<br />$ $
* $\mu$や$\lambda$にも不確かさがある
    * $\mu$（平均値）は200[mm]とは限らない
    * $\sqrt{1/\lambda}$（標準偏差）はセンサの性能を考えると$1\sim 10$[mm]くらいだろう<br />$ $
* つまり
    * <span style="color:red">$\mu, \lambda$も確率分布$p(\mu, \lambda)$で表現</span>
    * <span style="color:red">$\mu, \lambda$は推定対象</span>

---

### 自己位置推定との類似性

* 今扱っている問題: 結果から原因に関する変数/パラメータの分布を推定
    * 結果: センサ値
    * 原因: センサの特性
* 自己位置推定:
    * 結果: センサ値
    * 原因: 姿勢<br />$ $
* 類似点、相違点
    * 類似点: 原因に関する確率分布（<span style="color:red">=信念分布</span>）を求める<br />$\rightarrow$<span style="color:red">ベイズの定理</span>
    * 相違点: 姿勢は動くがセンサのパラメータは動かない<br />（マルコフ連鎖の式は不要）

---

### 確率分布$p(\mu, \lambda)$の更新

* センサ値が$z_0, z_1, \dots$と入ってきた
    * ベイズの定理でセンサ値$z_0$を反映すると次のようになる
        * $p(\mu, \lambda | z_0) = \dfrac{p(z_0 | \mu, \lambda)p(\mu, \lambda)}{p(z_0)} = \eta p(z_0 | \mu, \lambda)p(\mu, \lambda)$<br />$ $
* この式でセンサ値を次々に反映していけるが・・・
   * $p(\mu, \lambda | z_0)$と$p(\mu, \lambda)$が同じ確率モデルで表せないと面倒
        * カルマンフィルタのように線形化の山になるし、それでいい保証もない
        * とりあえず同じになる分布を仮定
   * $p(\mu, \lambda)$と$p(\mu, \lambda | z_0)$をそれぞれ<span style="color:red">事前分布</span>、<span style="color:red">事後分布</span>と呼ぶ

---

### 共役な分布

* 信念を<span style="color:red">ガウス-ガンマ分布</span>で作る
    * $p(\mu, \lambda) = b_0(\mu, \lambda | \mu_0, \zeta_0, \alpha_0, \beta_0) = \mathcal{N}(\mu | \mu_0, (\zeta_0\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_0, \beta_0)$
         * $\mu_0, \zeta_0, \alpha_0, \beta_0$: 信念分布のパラメータ
         * $\text{Gam}(\lambda |\alpha,\beta) = \dfrac{1}{\Gamma(\alpha)}\beta^\alpha \lambda^{\alpha-1} e^{-\beta\lambda}$
         * $\Gamma(\alpha) = \int_0^{\infty} t^{\alpha-1}e^{-t}dt$<br />$ $
* $p(z_0 | \mu, \lambda)$がガウス分布の場合、ベイズの定理を何度適用してもガウス-ガンマ分布に
    * $b_0(\mu, \lambda | \mu_0, \zeta_0, \alpha_0, \beta_0)$: ガウス分布に対する<span style="color:red">共役事前分布</span>


---

### 事前分布の作成

* $b_0(\mu, \lambda | \mu_0, \zeta_0, \alpha_0, \beta_0) = \mathcal{N}(\mu | \mu_0, (\zeta_0\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_0, \beta_0)$の$\mu_0, \zeta_0, \alpha_0, \beta_0$に適切な値を入れる
    * $\sqrt{1/\lambda}$（標準偏差）はセンサの性能を考えると$1\sim 10$[mm]くらいだろう<br />
$\rightarrow 10^{-2} < \lambda \le 1$<br />
$\rightarrow \lambda$の平均はおよそ$0.5$、分散はおよそ$0.5^2$<br />
$\rightarrow$ガンマ分布の性質から$\alpha_0 = 1, \beta_0 = 2$
    * $\mu$（平均値）は200[mm]くらい$\rightarrow \mu_0 = 200$
    * $\mu$のばらつきは$\sqrt{1/\lambda}$としましょう$\rightarrow\zeta_0 = 1$

---

### 事後分布の導出（1/3）

* ガウス-ガンマ分布でベイズの定理の式を表現
    * $\mathcal{N}(\mu | \mu_1, (\zeta_1\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_1, \beta_1) \\\\ = \eta \mathcal{N}(z_0 | \mu, \lambda^{-1}) \mathcal{N}(\mu | \mu_0, (\zeta_0\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_0, \beta_0)$
* さらにセンサ値$z_1, z_2, z_N$を反映していく
    * $\mathcal{N}(\mu | \mu_N, (\zeta_N\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_N, \beta_N) \\\\ = \eta \prod_{i=0}^{N-1} \mathcal{N}(z_i | \mu, \lambda^{-1}) \mathcal{N}(\mu | \mu_0, (\zeta_0\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_0, \beta_0)$

---

### 事後分布の導出（2/3）

* 右辺のガウス分布を整理（テキスト参照のこと）
    * $\prod\_{i=0}^{N-1} \mathcal{N}(z\_i | \mu, \lambda^{-1})\mathcal{N}(\mu | \mu\_0, (\zeta\_0\lambda)^{-1} )  = \cdots \\\\ = \\mathcal{N} \\left( \\mu \\Bigg| \\dfrac{1}{N+\\zeta_0}\\sum_{i=0}^{N-1}z_i + \\dfrac{\\zeta_0}{N+\\zeta_0} \\mu_0, [(N + \\zeta_0)\\lambda]^{-1} \\right) \\eta \\lambda^{N/2}\\exp\\left( -\\dfrac{1}{2}\\lambda U \\right)$
        * ここで、$U = \\sum_{i=0}^{N-1}z_i^2 + \\zeta_0\\mu_0^2 - (N+\\zeta_0) \\left(\\dfrac{1}{N+\\zeta_0}\\sum_{i=0}^{N-1}z_i + \\dfrac{\\zeta_0}{N+\\zeta_0} \\mu_0 \\right)^2$
* 上の結果と事後分布のガウス分布の部分$\mathcal{N}(\mu | \mu_N, (\zeta_N\lambda)^{-1} )$を比較すると
    * $\mu_N = \dfrac{1}{N+\zeta_0}\sum_{i=0}^{N-1}z_i + \dfrac{\zeta_0}{N+\zeta_0} \mu_0$
    * $\zeta_N = N + \zeta_0$
    * $U = \sum_{i=0}^{N-1}z_i^2 + \zeta_0\mu_0^2 - \zeta_N\mu_N^2$

---

### 事後分布の導出（3/3）

* 今度はガンマ分布同士を比較
    * $\text{Gam}(\lambda | \alpha_N, \beta_N) = \eta \lambda^{N/2} \exp\left( -\dfrac{1}{2}\lambda U \right) \text{Gam}(\lambda | \alpha_0, \beta_0) \\\\
\cdots \\\\
\\lambda^{\\alpha_N-1} \\exp(-\\beta_N\\lambda) = \\eta \\lambda^{N/2+\\alpha_0-1} \\exp\\left\\{-\\left( \\dfrac{1}{2} U + \\beta_0 \\right)\\lambda\\right\\}$
* 両辺の比較により
    * $\alpha_N = \dfrac{N}{2} + \alpha_0$
    * $\beta_N = \dfrac{1}{2}U + \beta_0 = \dfrac{1}{2} \left( \sum_{i=0}^{N-1}z_i^2 + \zeta_0\mu_0^2 \right) - \dfrac{1}{2}\zeta_N\mu_N^2 + \beta_0$

---

### 事後分布のパラメータ（まとめ）

* $\mathcal{N}(\mu | \mu_N, (\zeta_N\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_N, \beta_N)$
    * $\zeta_N = N + \zeta_0$
        * $\zeta_N$: $\zeta_0$個分水増しされたセンサ値の数
    * $\mu_N = \dfrac{1}{N+\zeta_0}\sum_{i=0}^{N-1}z_i + \dfrac{\zeta_0}{N+\zeta_0} \mu_0$
        * $\mu_N$: $\zeta_0$個のセンサ値$\mu_0$で水増しされたセンサ値の平均値
    * $\alpha_N = 2/N + \alpha_0$
        * センサ値一つに対して$0.5$ずつ増える何かの値
    * $\beta_N = \dfrac{1}{2}U + \beta_0 = \dfrac{1}{2} \left( \sum_{i=0}^{N-1}z_i^2 + \zeta_0\mu_0^2 \right) - \dfrac{1}{2}\zeta_N\mu_N^2 + \beta_0$
        * $\beta_N$: 水増ししたセンサ値から求めた分散に$\zeta_N/2$をかけて$\beta_0$を足したもの
        * $\lambda$の平均値$\alpha_N/\beta_N$は、$\beta_0$を無視すると、センサ値から求めた分散の逆数を$\zeta_N/2$で割って$\alpha_N$をかけたもの$\rightarrow$ほぼセンサ値から求めた分散の逆数（つまりセンサ値の精度）
        * $\lambda$の分散$\alpha_N/\beta_N^2$は小さくなって$\lambda$の値が確定していく
        * $\zeta_N\lambda$は増え続け、$\mu$の精度が高くなっていく

---

### ベイズ推論の実装

* 左図: 共役事前分布からセンサ値の分布を一つドロー（[コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_inference/gauss_gamma0.ipynb)）
* 右図: センサ値を5個入力してセンサ値の分布を一つドロー（[コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_inference/gauss_gamma1.ipynb)）
* 右図: センサ値をすべて入力してセンサ値の分布を一つドロー（[コード](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_inference/gauss_gamma2.ipynb)）

<img width="30%" src="../figs/bayes_inference_init.png" />
<img width="30%" src="../figs/bayes_inference_n5.png" />
<img width="30%" src="../figs/bayes_inference_fin.png" />


---

### A.2 変分推論による混合モデルの解析

* やること: 2章で出てきた下の分布に<span style="color:red">複数の</span>ガウス分布をあてはめる
    * 今度はマルチモーダル

<img width="50%" src="../figs/sensor_histgram_600.png" />

---

### センサ値の生成モデル

* 解析をするにあたって次のような仮定を置く
    * 時系列は無視
    * 特定の条件（外光、湿度、気温など）に対応した何種類かのガウス分布がある. 
    * ガウス分布は$K$個存在する. （以後$k=0,1,2,\dots,K-1$と番号をつける. ）
    * 各ガウス分布の平均, 精度はそれぞれ$\mu_k, \lambda_k$. 
    * 各センサ値$z_i$は, ガウス分布のどれかから生成されたものと考える. 
    * 一つのセンサ値が生成されるとき, $k$番目のガウス分布から生成される確率は$\pi_k$である. <br />$ $ 
* $z_{0:N-1}$から各分布の$\mu_k, \lambda_k, \pi_k$を推定したい

---

### 生成モデルの数式化

* 一つのセンサ値$z$をドローするプロセスを考える
    * ガウス分布が選ばれるプロセス 
        * $\boldsymbol{c} \sim \text{Cat}(\boldsymbol{c} | \boldsymbol{\pi})$
            * $\boldsymbol{c}$は$k$次元で、一つが1であとは0のベクトル
            * 1のある番目のガウス分布が選ばれたということを意味する
            * 1-of-K 符号化法


* <span style="color:red">混合ガウス分布</span>
