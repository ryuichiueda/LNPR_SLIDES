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
        * $\lambda$は精度
* $\mu$や$\lambda$にも不確かさがある
    * $\mu$（平均値）は200[mm]とは限らない
    * $\sqrt{1/\lambda}$（標準偏差）はセンサの性能を考えると$1\sim 10$[mm]くらいだろう
