$\newcommand{\V}[1]{\boldsymbol{#1}}$
$\newcommand{\jump}[1]{[\\![#1]\\!]}$
$\newcommand{\bigjump}[1]{\big[\\!\\!\big[#1\big]\\!\\!\big]}$
$\newcommand{\Bigjump}[1]{\bigg[\\!\\!\bigg[#1\bigg]\\!\\!\bigg]}$
$\\newcommand{\\indep}{\\mathop{\\perp\\!\\!\\!\\perp}}$

# 2. 確率・統計の基礎（後半下）

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 2.5 多次元のガウス分布

---

## 2.5.1 2次元のガウス分布の<br />当てはめ


* 右: 700[mm], 12〜16時台の<br />光センサとLiDARの値の分布
    * それぞれ$x,y$と表して<br />$\V{z} = (x \ y)^T$を考える
    * $P(\V{z})= P(x,y)$は<br />どちらの変数を周辺化しても<br />ガウス分布に当てはまりがよい

<img width="30%" src="./figs/lidar_light_700.png" />

同時確率分布$P(\V{z})$はどんな数式で表現できるか？

---

### 多次元のガウス分布

* 次のような確率密度関数（$n$次元ガウス分布）を作ると、<br />どの変数を残して周辺化してもガウス分布に
    * $$\mathcal{N}(\boldsymbol{z} | \boldsymbol{\mu}, \Sigma) = \frac{1}{(2\pi)^{\frac{n}{2}}\sqrt{|\Sigma|}} \exp \left\\{-\frac{1}{2}(\boldsymbol{z}-\boldsymbol{\mu})^T\Sigma^{-1}(\boldsymbol{z}-\boldsymbol{\mu})\right\\}$$
    $$ = \eta \exp \left\\{-\frac{1}{2}(\boldsymbol{z}-\boldsymbol{\mu})^T\Sigma^{-1}(\boldsymbol{z}-\boldsymbol{\mu})\right\\}$$
        * $\boldsymbol{\mu}$: 中心（$n$次元）
        * $\Sigma$: <span style="color:red">共分散行列</span>（$n \times n$対称行列）
    * 形状: 前のスライドの分布のように、等高線を引くと楕円に

共分散行列って何？

---

### 共分散行列

* 2次元の場合、次のような行列
    * $\Sigma = \begin{pmatrix}\sigma_x^2 & \sigma_{xy} \\\\ \sigma_{xy} & \sigma_y^2\end{pmatrix}$
        * $\sigma_x^2, \sigma_y^2$: $x, y$の分散、$\sigma_{xy}$: <span style="color:red">共分散</span><br />　
* $\sigma_{xy} = \frac{1}{N}\sum_{i=0}^{N-1}(x_i-\mu_x)(y_i-\mu_y)$
    * なんなのかはあとで

イメージが難しいので<br />センサ値から作って理解しましょう

---

### 2次元ガウス分布の当てはめ

* 光センサ、LiDARのセンサ値を2次元ガウス分布に
    * $\boldsymbol{\mu} =$<span style="font-size:80%">$ \frac{1}{N}\sum_{i=0}^{N-1} \boldsymbol{z}_i = (19.9 \ 729.3)^T$</span>
    * $\Sigma =$<span style="font-size:80%">$ \begin{pmatrix}\sigma_x^2 & \sigma_{xy} \\\\ \sigma_{xy} & \sigma_y^2\end{pmatrix} = \begin{pmatrix}42.1 & -0.3 \\\\ -0.3 & 17.7\end{pmatrix}$</span>

<img width="30%" src="./figs/lidar_light_700.png" />
$\rightarrow$
<img width="40%" src="./figs/2d_gauss.png" />

---

## 2.5.2 共分散の意味

* 式（再掲）
    * $\sigma_{xy} = \frac{1}{N}\sum_{i=0}^{N-1}(x_i-\mu_x)(y_i-\mu_y)$
        * $x_i -\mu_x$と$y_i -\mu_y$の符号の多くが<br />一致していると正、不一致だと負に<br />　
* 共分散が正、負になると<br />楕円が傾く（図）
    * 左: $\sigma_{xy}=25\sqrt{3}$
    * 右: $\sigma_{xy}=-25\sqrt{3}$<br />
     （どちらも$\sigma_x^2 = 100, \sigma_y^2 = 50$）

<img width="35%" src="./figs/2d_gausses.png" />

$x$が大きいと$y$が大きく/小さくなりやすいという<br />傾向を示す

---

### 例

* 左: 距離200[mm]のときの光センサとLiDARのセンサ値
    * 光センサの値が大きいとLiDARの値が小さい傾向あり
        * （ちょっと形が悪いので例としては良くない）
    * 共分散を計算すると$\sigma_{xy} = -13.4$（負の値）<br />　
* 右: ガウス分布に（むりやり）当てはめて描画したもの
    * 右下がりに傾く

<img width="30%" src="./figs/sensor_2d_200.png" />
<img width="35%" src="./figs/gauss_2d_200.png" />

---

## 2.5.3 共分散行列と誤差楕円

* 共分散行列は対角行列を回転行列で挟んだ形にできる
    * 左のガウス分布:
        * $\Sigma = \begin{pmatrix} 100 & 25\sqrt{3} \\\\ 25\sqrt{3} & 50 \end{pmatrix} =         R_{\pi/6} \begin{pmatrix} 125 & 0 \\\\ 0 & 25 \end{pmatrix} R_{\pi/6}^{-1}$
    * 右のガウス分布:
        * $\Sigma = \begin{pmatrix} 100 & -25\sqrt{3} \\\\ -25\sqrt{3} & 50 \end{pmatrix} =         R_{-\pi/6} \begin{pmatrix} 125 & 0 \\\\ 0 & 25 \end{pmatrix} R_{-\pi/6}^{-1}$
<img width="40%" src="../figs/2d_gausses.png" />
    * 等高線は楕円を回転させたもの
    * <span style="color:red">長軸、短軸の長さ $\propto$ 固有値（= その方向の標準偏差）</span>

---

### 誤差楕円

* 長軸、短軸の長さが各軸の標準偏差の$\alpha$倍の楕円
    * $\alpha$を定数とすると楕円内の確率が一定値に<br />
    $\Longrightarrow$大きさでガウス分布の不確かさが比較可能
    * 本書の図では$\alpha=3$
        * 2次元の場合、内部の確率は$0.99$

<img width="38%" src="./figs/kalman_30_1.png" />

---

## 2.5.4 変数の和に対するガウス分布の合成

* 次のような問題を考える
    * 変数$\V{x}_1, \V{x}_2$がそれぞれガウス分布にしたがう
    * このとき$\V{x}_3 = \V{x}_1 + \V{x}_2$の分布は？<br />　
* このような問題がロボットで発生する例
    * ロボットが1[m]動くとき、行き先がガウス分布でばらつく
    * ロボットがもう1[m]動いたら最終的な分布はどうなるか？

計算しましょう

---

### 計算

* 注意: 書籍と違う方法でやってみます<br />　
* $\V{x}_1, \V{x}_2$の分布を考える
    * $\V{x}_1 \sim \mathcal{N}_1(\V{x} | \V{\mu_1},\Sigma_1)$
    * $\V{x}_2 \sim \mathcal{N}_2(\V{x} | \V{\mu_2},\Sigma_2)$<br />　
* $\V{x}_3$の分布は次のような考えで作る
    * $p(\V{x}\_3) = \jump{p(\V{x}\_3, \V{x}\_1)}\_{\V{x}\_1} = \jump{p(\V{x}\_3 | \V{x}\_1) p(\V{x}\_1)}\_{\V{x}\_1}$
        * $p(\V{x}_1)$というのは$\mathcal{N}_1$のこと
        * $p(\V{x}\_3 | \V{x}\_1)$の値は$\mathcal{N}_2(\V{x}_3 - \V{x}_1 | \V{x}_1, \V{\mu}_2, \Sigma_2)$に等しい
            * <span style="color:red">$\because \V{x}_1$のときに$\V{x}_3$となるという事象は、<br />$\V{x}_1$のときに$\V{x}_2 = \V{x}_3 - \V{x}_1$となる事象と同じ</span>

$\V{p}(\V{x}_3)$の式に$\mathcal{N}_1, \mathcal{N}_2$を代入（次ページ）

---

### 計算（続き）

* ここからは書籍と同じ
    * $p(\V{x}\_3) = \eta \bigjump{ e^{-\frac{1}{2} (\V{x}\_3-\V{x}\_1-\V{\mu}\_2)^\top\Sigma\_2^{-1}( \V{x}\_3-\V{x}\_1-\V{\mu}\_2  )}e^{-\frac{1}{2} (\V{x}\_1-\V{\mu}\_1)^\top\Sigma\_1^{-1}(\V{x}\_1-\V{\mu}\_1)} }\_{\V{x}\_1}$<br />
$= \eta \bigjump{ e^{-\frac{1}{2} (\V{x}\_3-\V{x}\_1-\V{\mu}\_2)^\top\Sigma\_2^{-1}( \V{x}\_3-\V{x}\_1-\V{\mu}\_2  )-\frac{1}{2} (\V{x}\_1-\V{\mu}\_1)^\top\Sigma\_1^{-1}(\V{x}\_1-\V{\mu}\_1)} }\_{\V{x}\_1}$<br />
$= \cdots$（付録B.1.9の方法で$\V{x}_1$の積分が除去できる）<br />
$= \eta e^{-\frac{1}{2} (\V{x}\_3-\V{\mu}\_1-\V{\mu}\_2)^\top(\Sigma\_1 +\Sigma\_2)^{-1}( \V{x}\_3-\V{\mu}\_1-\V{\mu}\_2  )}$
<span style="color:red">$= \mathcal{N}(\V{x}\_3 | \V{\mu}\_1+\V{\mu}\_2, \Sigma_1 + \Sigma_2)$</span>

---

### 補足

* 情報行列
