$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 2. 確率・統計の基礎（後半）

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 2.4 複雑な分布

* これまでひとつのセンサの値だけを扱ってきたが、<br />センサの値は他の要因で変わる
    * 壁までの距離、向き、その他センサに関する変数・・・ <br />　

<div style="color:red">
ほとんどの場合、もっと多くの変数の考慮が必要
</div>

---

## 2.4.1 条件付き確率


* センサ値のヒストグラム（距離: 600[<span style="text-transform:none">mm</span>])
    * ピーク（統計では<span style="color:red">モード</span>）が2つ
        * <span style="color:red">マルチモーダル</span>
    * 635[mm]の頻度がゼロ

<img width="45%" src="./figs/sensor_histgram_600.png" />

今までの解析方法では解析できない

---

### 時系列で見てみる

* センサ値が得られた順に並べてグラフを描画
    * どうやら時間で値が変動しているらしい
        * もっと言うと、昼と夜で変動するなにかが原因<br />（光、気温、湿度、・・・）

<img width="40%" src="./figs/sensor_600_time.png" />

時刻$t$が変数に

---

### 時間別のヒストグラムを作成

* 時間で<span style="color:red">条件付け</span>することでガウス分布となる
    * オレンジ: 昼の14時台
    * 青: 朝の6時台
<br />
<img width="60%" src="./figs/sensor600_6h_14h.png" />

「条件付けした分布、確率」というものがある

---

### 条件付き確率の表記

* 例えば6時台のセンサ値の確率分布を次のように表記
    * $P(z | t \in \text{6時台})$
        * $t \in \text{6時台}$: $t$が6時台の時刻の集合に含まれる<br />　
* 一般的な条件付き確率の表記
    * <span style="color:red">$P(y|x)$</span>: 変数$x$で条件付けられる変数$y$の分布
        * $x$が$y$に「直接」影響を与えている必要はない
            * 例: 時刻はセンサ値を変動させる直接の原因ではない

---

## 2.4.2 同時確率と<br />加法定理、乗法定理


---

### 2次元の確率分布（度数分布）

* 横軸: 時刻
* 縦軸: センサ値
* 2次元の確率分布$p(z,t)$（$z$: センサ値、$t$: 時刻）
    * <span style="color:red">同時分布、結合分布</span>と呼ばれる<br />　

![](./figs/sensor_600_2d.png)

---

<!--ここら辺から実例がないので話しづらい-->

### 2次元$\rightarrow$1次元
#### 周辺化

* ある変数に関する情報を消し去る
    * 例: $\ p(z) = \int_{-\infty}^{\infty} p(z,t) dt$
        * 時間の情報を消してセンサ値の1次元分布を作る

<img width="30%" src="../figs/sensor_600_2d.png" />
$\rightarrow$
<img width="40%" src="../figs/sensor_histgram_600.png" />

>>>

### 演習

* 同時分布のデータ$p(z,t)$からスタートして$p(z)$のグラフを描画してみましょう
    * [lidar_600.ipynb](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_sensor/lidar_600.ipynb) [6]-[11]

---

### 2次元$\rightarrow$1次元
#### 条件付き確率

* ある変数を一つに限定
    * 例: $p(z | t)$: ある時間帯$t$におけるセンサ値$z$の分布


<img width="30%" src="../figs/sensor_600_2d.png" />
$\rightarrow$
<img width="40%" src="../figs/sensor600_6h_14h.png" />

---

### 確率の乗法定理・加法定理

* 乗法定理
    * $p(z,t) = p(z|t)p(t) = p(t|z)p(z)$
        * 同時分布を条件つき確率と条件の積に
* 加法定理
    * $p(z) = \int_{-\infty}^{\infty} p(z,t) dt$
    * $p(t) = \int_{-\infty}^{\infty} p(z,t) dz$
        * 周辺化の根拠
* アルゴリズムの導出の際に頻出
    * 今はあまりピンと来ないかもしれない

>>>

### 演習

* 同時分布$p(a,b,c|d)$から$c$と$b$の分布を乗法定理で順に分離してみましょう
* $p(a|b)$に加法定理で変数$c$を追加して、さらに、積分内で$p(c)$の分布を乗法定理で分離してみましょう

---

### ベイズの定理

* 乗法定理から導出<br />
    * 乗法定理: $p(z,t) = p(z|t)p(t) = p(t|z)p(z)$
    * 整理すると<span style="color:red">$p(z|t) = \dfrac{p(t|z)p(z)}{p(t)} = \eta p(t|z)p(z)$</span>となる
        * $\eta$: 正規化定数
            * $\int_{-\infty}^{\infty}p(z|t)dt=1$とするための調整の定数
    * 意味
        * 時間帯$t$と、$z$がどの時間帯で得られやすいかが分かると、$z$の分布$p(z)$くらいにしか分からなかったのが$p(z|t)$まで分かるようになる<br />$ $
*  ベイズの定理もアルゴリズムの導出で出てきます

>>>

### 演習

* $p(z,t)$から$p(z|t)$の式を導出してみましょう

---

### 2次元のガウス分布

* 光センサとLiDARの分布（700[mm], 12〜16時台のデータ）
    * どちらの変数を周辺化してもガウス分布に当てはまる

<img width="40%" src="../figs/lidar_light_200.png" />

---

### 多次元のガウス分布

* 定義:
    * $$\mathcal{N}(\boldsymbol{z} | \boldsymbol{\mu}, \Sigma) = \frac{1}{(2\pi)^{\frac{n}{2}}\sqrt{|\Sigma|}} \exp \left\\{-\frac{1}{2}(\boldsymbol{z}-\boldsymbol{\mu})^T\Sigma^{-1}(\boldsymbol{z}-\boldsymbol{\mu})\right\\}$$
        * $n$: 次元、$\boldsymbol{\mu}$: 中心（$n$次元ベクトル）、$\Sigma$: 共分散行列（$n \times n$行列）
* $n=2, \boldsymbol{z} = (x \ y)^T$のときの共分散行列
    * $\Sigma = \begin{pmatrix}\sigma_x^2 & \sigma_{xy} \\\\ \sigma_{xy} & \sigma_y^2\end{pmatrix}$
        * $\sigma_x^2, \sigma_y^2$: それぞれ$x, y$の分散
        * $\sigma_{xy} = \frac{1}{N}\sum_{i=0}^{N-1}(x_i-\mu_x)(y_i-\mu_y)$: <span style="color:red">共分散</span>
* よく分からない。センサデータを当てはめてみましょう

---

### ガウス分布の当てはめ

* 2次元のデータ$\boldsymbol{z}_i = (x_i \ y_i)^T \ $<span style="font-size:70%">$(i=0,1,2,\dots,N-1)$</span>に対して
    * $\boldsymbol{\mu} = \frac{1}{N}\sum_{i=0}^{N-1} \boldsymbol{z}_i = (19.9 \ 729.3)^T$
    * $\Sigma = \begin{pmatrix}\sigma_x^2 & \sigma_{xy} \\\\ \sigma_{xy} & \sigma_y^2\end{pmatrix} = \begin{pmatrix}42.1 & -0.3 \\\\ -0.3 & 17.7\end{pmatrix}$

<img width="30%" src="../figs/lidar_light_200.png" />
$\rightarrow$
<img width="40%" src="../figs/2d_gauss.png" />

>>>

### 演習

* この図を描画してみましょう
    * [multi_gauss1.ipynb](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_sensor/multi_gauss1.ipynb) [1]-[5]


![](../figs/2d_gauss.png)

---

### 共分散の意味

* 正、負になると楕円が傾く
    * 共分散が正: 一方の値が大きい/小さいときに他方も大きい/小さい
        * 左のガウス分布: $\Sigma = \begin{pmatrix} 100 & 25\sqrt{3} \\\\ 25\sqrt{3} & 50 \end{pmatrix}$
    * 共分散が負: 正のときと逆
        * 右のガウス分布: $\Sigma = \begin{pmatrix} 100 & -25\sqrt{3} \\\\ -25\sqrt{3} & 50 \end{pmatrix}$ 

<img width="40%" src="../figs/2d_gausses.png" />

>>>

### 演習

* 2次元のガウス分布を描画してみましょう
    * [multi_gauss3.ipynb](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_sensor/multi_gauss3.ipynb)[1]


---

### 共分散行列の意味

* 共分散行列は対角行列を回転行列で挟んだ形にできる
    * 左のガウス分布:
        * $\Sigma = \begin{pmatrix} 100 & 25\sqrt{3} \\\\ 25\sqrt{3} & 50 \end{pmatrix} =         R_{\pi/6} \begin{pmatrix} 125 & 0 \\\\ 0 & 25 \end{pmatrix} R_{\pi/6}^{-1}$
    * 右のガウス分布:
        * $\Sigma = \begin{pmatrix} 100 & -25\sqrt{3} \\\\ -25\sqrt{3} & 50 \end{pmatrix} =         R_{-\pi/6} \begin{pmatrix} 125 & 0 \\\\ 0 & 25 \end{pmatrix} R_{-\pi/6}^{-1}$
<img width="40%" src="../figs/2d_gausses.png" />
    * 対角行列は長軸、短軸の長さの2乗を表す（固有値）
    * 回転行列は固有ベクトルの組み合わせ

>>>

### 演習

* 共分散行列$\Sigma = \begin{pmatrix} 100 & -25\sqrt{3} \\\\ -25\sqrt{3} & 50 \end{pmatrix}$を固有値分解して、ガウス分布の長軸・短軸を描画してみましょう。
    * [multi_gauss3.ipynb](https://github.com/ryuichiueda/LNPR_BOOK_CODES/blob/master/section_sensor/multi_gauss3.ipynb)[2]-[3]
