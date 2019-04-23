## 2. 確率統計の基礎

千葉工業大学 上田 隆一
2019年4月24日

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 実験

* ロボットを決めた距離だけ壁から離して置く
* 3秒ごとにセンサの値を記録（2日間）
    * 光センサとLiDARの1本のレーザー

<img width="30%" src="../figs/sensor_experiment.jpg" />

---

### 得られたデータ

* [200mm](https://raw.githubusercontent.com/ryuichiueda/LNPR_BOOK_CODES/master/section_sensor/sensor_data_200.txt)
    * $1: 日付
    * $2: 時分秒
    * $3: 光センサの値
    * $4: LiDAR（の1本のレーザの）値
* LiDARの値の特性を調査してみましょう
    * こんなにたくさんデータあるけどどうしよう？
        * 統計学
    * 以後、LiDARから得た値を「センサ値」と呼称

---

### 度数分布・ヒストグラム

* 度数分布
    * センサ値の範囲をいくつかの区間に分ける
    * 各区間に属するセンサ値の数を集計
* ヒストグラム
    * 度数分布をグラフにしたもの
    * 下図: 区間の幅1で描いたヒストグラム

<img width="50%" src="../figs/sensor_200_histgram.png" />

---

### センサ値を予想する

* 度数分布からセンサが次に出す値を予想できるか？
    * だいたいどのような値になるかは予想可能
        * センサ値を何度も予想したら頻度が度数分布の示す割合に
            * センサ値が$z$になる割合: 「$z$になる頻度/データ数」<span style="color:red">$\Longrightarrow$確率</span>
    * 「$z$になる頻度/データ数」を返す関数$p(z)$<br /><span style="color:red">$\Longrightarrow$確率質量関数（確率分布）</span>
* 確率分布が分かる: センサの特性を統計的に理解

<img width="40%" src="../figs/sensor_200_histgram.png" />

---

### 素朴な確率分布

* 作り方
    1. 度数分布の頻度を全て足す
    2. 各区間の頻度を1の合計値で割る
        * 合計が1になる$\rightarrow$これを 確率分布$p(z)$とする（左図）
* センサ値$z$を$p(z)$から選ぶ操作: $z \sim p(z)$
    * 「ドローする」と表現
    * 右図: 左図の$p(z)$からドローを繰り返して作った度数分布

<img width="40%" src="../figs/prob_200.png" />
<img width="40%" src="../figs/simulated_sensor_200.png" />

---

### 確率分布のモデル

* 度数分布から確率分布を作るのが唯一の方法？
   * 度数分布のデコボコは重要なのか？
       * データの回数が少ないともっとデコボコ
   * 実際に得られた値より小さな/大きな値になる確率はゼロ？
   * 何か演算をするときに度数分布ベースだと面倒なことも
   * 様々な裏付けから、<span style="color:red">確率分布が従う数式</span>というものが存在

$\Longrightarrow$確率分布のモデル

---

### ガウス分布（正規分布）

* 確率密度関数$\ p(x | \mu, \sigma^2 ) = \dfrac{1}{\sqrt{2\pi}\sigma} \exp\left[ - \dfrac{(x - \mu)^2}{2\sigma^2} \right]$
    * 値は確率でなく<span style="color:red">密度</span>。積分すると確率
    * $\mathcal{N}(x | \mu, \sigma^2)$と表す
* $N$個のデータ$z_i$ <span style="font-size:70%">$(i=0,1,2,\dots,N-1)$</span>からの計算方法
    * $\mu = \frac{1}{N}\sum_{i=0}^{N-1} z_i$: 分布の中心
    * $\sigma^2 = \frac{1}{N-1}\sum_{i=0}^{N-1} (z_i - \mu)^2$: 分布の分散

<img width="40%" src="../figs/gauss_200.png" />

---

### 期待値

* 無限にセンサ値をドローしたときの平均値
    * $\langle z \rangle_{p(z)}$と表現
    * 実際にドローしなくても$\langle z \rangle_{p(z)} = \int_{-\infty}^{\infty} zp(z) dz$で計算可能
* 一般的な期待値の定義
    * $\langle f(z) \rangle_{p(z)} = \int_{-\infty}^{\infty} f(z)p(z) dz$
* ガウス分布の性質
    * $\langle z \rangle_{p(z)} = \mu$
    * $\langle (z - \mu)^2 \rangle_{p(z)} = \sigma^2$

---

### ここまでのまとめ

* LiDARからのデータをガウス分布としてモデル化した
    * 多くのデータから予想される確率分布を$\mu$と$\sigma^2$だけで表現
    * なんでセンサの値がばらつくかは分析していないけど、どのようにばらつくかは分析できた
* 確率分布やその他統計的性質が分かると何ができるか？
    * センサ値が得られたときに、それを鵜呑みにしないこと
        * どれだけばらつくか分かる
    * センサ値が複数得られたときに真の値を予測できる
        * 次章以降

---

### もっと複雑な場合

* これまではセンサの値だけを考えていましたが・・・
    * センサの値は他の要因で変わる
        * 壁までの距離、向き、・・・ 

<div style="color:red">
$\Longrightarrow$
ほとんどの場合、確率分布は<br />多次元で考えないといけない
</div>

---

### センサ値のヒストグラム<br />（距離: 600[<span style="text-transform:none">mm</span>])

<img width="60%" src="../figs/sensor_histgram_600.png" />

なんだこれは？

---

### 時系列で見てみる

* センサ値が得られた順に並べてグラフを描画
    * どうやら時間で値が変動しているらしい
        * もっと言うと、おそらく外からの光だと思われる

<img width="40%" src="../figs/sensor_600_time.png" />

---

### 時間別のヒストグラム

* オレンジ: 昼の14時台
* 青: 朝の6時台
<br />
<img width="100%" src="../figs/sensor600_6h_14h.png" />

分けるとどちらもガウス分布状に

---

### 2次元の確率分布（度数分布）

* 横軸: 時刻
* 縦軸: センサ値
* 2次元の確率分布$p(z,t)$（$z$: センサ値、$t$: 時刻）
<br />
<img width="50%" src="../figs/sensor_600_2d.png" />

---

### 2次元$\rightarrow$1次元
#### 周辺化

* ある変数に関する情報を消し去る
    * 例: $\ p(z) = \int_{-\infty}^{\infty} p(z,t) dt$
        * 時間の情報を消してセンサ値の1次元分布を作る

<img width="30%" src="../figs/sensor_600_2d.png" />
$\rightarrow$
<img width="40%" src="../figs/sensor_histgram_600.png" />

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
    * $p(z) = \int_{-\infty}^{\infty} p(z,t) dt, p(t) = \int_{-\infty}^{\infty} p(z,t) dz$
        * 周辺化の根拠
* アルゴリズムの導出の際に頻出
    * 今はあまりピンと来ないかもしれない

---

### ベイズの定理

* 乗法定理から導出<br />
$\begin{align}
p(z,t) &= p(z|t)p(t) = p(t|z)p(z) \Longrightarrow& \\\\
p(z|t) &= \dfrac{p(t|z)p(z)}{p(t)} = \eta p(t|z)p(z) \quad (\eta: \text{正規化定数})
\end{align}$
    * 意味: 時間帯$t$と、$z$がどの時間帯で得られやすいかが分かると、$z$の分布$p(z)$くらいにしか分からなかったのが$p(z|t)$まで分かるようになる
    * 正規化定数$\eta$は$\int_{-\infty}^{\infty}p(z|t)dt=1$とするための調整の定数
*  ベイズの定理もアルゴリズムの導出で出てきます

---

### 2次元のガウス分布

* 光センサとLiDARの分布（700[mm], 12時台〜16時台のデータ）
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

---

### 共分散の意味

* 正、負になると楕円が傾く
    * 共分散が正: 一方の値が大きい/小さいときに他方も大きい/小さい
        * 左のガウス分布: $\Sigma = \begin{pmatrix} 100 & 25\sqrt{3} \\\\ 25\sqrt{3} & 50 \end{pmatrix}$
    * 共分散が負: 正のときと逆
        * 右のガウス分布: $\Sigma = \begin{pmatrix} 100 & -25\sqrt{3} \\\\ -25\sqrt{3} & 50 \end{pmatrix}$ 

<img width="40%" src="../figs/2d_gausses.png" />

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

