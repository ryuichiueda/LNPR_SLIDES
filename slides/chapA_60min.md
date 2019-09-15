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

---

### ガウス-ガンマ分布

* 信念を<span style="color:red">ガウス-ガンマ分布</span>で作る
    * $p(\mu, \lambda) = b_0(\mu, \lambda | \mu_0, \zeta_0, \alpha_0, \beta_0) = \mathcal{N}(\mu | \mu_0, (\zeta_0\lambda)^{-1} ) \text{Gam}(\lambda | \alpha_0, \beta_0)$
         * $\mu_0, \zeta_0, \alpha_0, \beta_0$: 信念分布のパラメータ
         * $\text{Gam}(\lambda |\alpha,\beta) = \dfrac{1}{\Gamma(\alpha)}\beta^\alpha \lambda^{\alpha-1} e^{-\beta\lambda}$
         * $\Gamma(\alpha) = \int_0^{\infty} t^{\alpha-1}e^{-t}dt$<br />$ $
    * $p(z_0 | \mu, \lambda)p(\mu, \lambda)$がガウス分布の場合、ベイズの定理を何度適用してもガウス-ガンマ分布に


