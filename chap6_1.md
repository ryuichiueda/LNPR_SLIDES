$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 6. カルマンフィルタによる自己位置推定（前半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### カルマンフィルタ

* 信念分布をガウス分布に限定したベイズフィルタ
    * 次のベイズフィルタの式をすべてガウス分布の演算に
        * 移動時: $\hat{b}\_t(\V{x}) =  \big\langle p(\V{x} | \V{x}', \V{u}_t) \big\rangle_\{b_\{t-1\}(\V{x}')\}$ 
        * 観測時: $b\_t(\V{x}) = \eta p(\textbf{z}\_t | \V{x}) \hat{b}\_t(\V{x})$<br />　
* 本書で扱う系は<span style="color:red">非線形系</span><br />$\Longrightarrow$<span style="color:red">線形化</span>という操作が必要
    * 線形化をともなうカルマンフィルタ: 「拡張カルマンフィルタ」
    * 本書でいうカルマンフィルタは拡張カルマンフィルタのこと

なぜガウス分布にこだわるのか？

---

### カルマンフィルタの性質

パーティクルフィルタと比較すると・・・

* <span style="color:red">計算量が小さい</span>
    * 平均値と共分散行列の演算だけでベイズフィルタを実装可能
    * 状態の次元が高くても適用可能<br />　
* ガウス分布なので次のような状況で信念が表現しずらい
    * ロボットが壁ぎわにいて、壁の向こうにはいる可能性がない
        * 壁のところで分布を打ち切れない
    * 分布がマルチモーダル
    * 自己位置の情報がない（一様分布）<br />　
* （線形化が必要な場合に）誤差が生じる

---

## 6.1 信念分布の近似と描画

* 信念をガウス分布として実装
    * $b_t = \mathcal{N}(\V{\mu}_t, \Sigma_t)$<br />　
* 誤差楕円を描く
    * 2章で扱いました
    * $XY\theta$空間の楕円球となる
        * （ユークリッド空間とみなせば）<br />　
    * シミュレータでは次の2要素で描画
        * $XY$平面の$3\sigma$範囲を表す楕円
        * $\theta$方向の$3\sigma$範囲を示す2本の線分

<img width="40%" src="./figs/belief_ellipse.png" />

---

## 6.2 移動後の信念分布の更新

* やること: 移動時のベイズフィルタの式をガウス分布だけの演算に
    * <span style="color:red">近似が必要</span><br />　
* ベイズフィルタの式
    * $\hat{b}\_t(\V{x}) = \big\langle p(\V{x} | \V{x}', \V{u}\_t) \big\rangle\_{b\_{t-1}(\V{x}')} $<span style="color:red">$= \int\_{\V{x}' \in \mathcal{X}} p(\V{x} | \V{x}', \V{u}\_t) b\_{t-1}(\V{x}') d\V{x}'$</span><br />　

$p(\V{x} | \V{x}', \V{u}\_t)$の形が問題になる

---

## 6.2.1 状態遷移モデルの線形化

* 問題: 分布$p(\V{x} | \V{x}', \V{u}\_t)$がガウス分布にはならない
    * 例: 前章では$\V{u}_t$のばらつきをガウス分布でモデル化したが、<br />ロボットをまっすぐ走らせるとパーティクルは弓状の分布に

<img width="30%" src="./figs/simulated_on.png" />
<img width="40%" src="./figs/nonliner_motion.jpg" />

むりやりガウス分布に近似しましょう

---

### 近似の方法（状態遷移関数の線形化）

* 移動後の姿勢$\V{x}\_t$の分布がガウス分布になるように、<br />状態方程式を次のような形式に<span style="color:red">線形近似</span>
    * $\V{x}\_t = \V{f}(\V{x}\_{t-1}, \V{u}\_t')$ <span style="color:red">$\approx \V{f}(\V{x}\_{t-1}, \V{u}\_t) + A\_t (\V{u}\_t' - \V{u}\_t)$</span>
        * $\V{u}\_t, \V{u}'_t$: 制御指令と実際の制御
        * $A_t$: 後述

<img width="60%" src="./figs/motion_linerized.jpg" />

速度、角速度の誤差が等倍に広がる

---

### 行列$A_t$の意味

* $A\_t = \dfrac{\partial \V{f}}{\partial \V{u}}\Big|\_{\V{x}=\V{x}\_{t-1},\V{u}=\V{u}\_t}$
    * 近似した状態方程式（再掲）: $\V{x}\_t \approx \V{f}(\V{x}\_{t-1}, \V{u}\_t) + A\_t (\V{u}\_t' - \V{u}\_t)$<br />　
* 解釈
    * <span style="color:red">$\V{x} =\V{x}_{t-1}, \V{u} = \V{u}_t$において、$\V{u}$が少しずれると$\V{f}$がどれだけずれるかを計算したもの</span>
    * $A_t$に$\V{u}'_t - \V{u}$（速度・角速度の誤差）をかけることで、$\V{f}$のズレ（$=XY\theta$空間での$\V{x}_t$の誤差）が計算できる<br />　

---

### 行列$A_t$の計算

* 状態方程式
    * <span style="font-size:70%">$\\boldsymbol{f}(\\boldsymbol{x}, \\boldsymbol{u}) = \\begin{pmatrix} x \\\\ y \\\\ \\theta \\end{pmatrix} + \\begin{pmatrix} \\nu\\omega^{-1}\\left\\{\\sin( \\theta + \\omega \\Delta t ) - \\sin\\theta \\right\\} \\\\ \\nu\\omega^{-1}\\left\\{-\\cos( \\theta + \\omega \\Delta t ) + \\cos\\theta \\right\\} \\\\ \\omega \\Delta t \\end{pmatrix}$</span>
* 状態方程式の偏微分
    * <span style="font-size:70%">$\\dfrac{\\partial \\boldsymbol{f}}{\\partial \\boldsymbol{u}} = \\begin{pmatrix} \\partial f\_x/\\partial \\nu & \\partial f\_x/\\partial \\omega \\\\ \\partial f\_y/\\partial \\nu & \\partial f\_y/\\partial \\omega \\\\ \\partial f\_\\theta/\\partial \\nu & \\partial f\_\\theta/\\partial \\omega \\end{pmatrix} \\nonumber \\\\ \hspace{-5em} = \\begin{pmatrix} \\omega^{-1}\\left\\{\\sin( \\theta + \\omega \\Delta t ) - \\sin\\theta \\right\\} & -\\nu\\omega^{-2}\\left\\{\\sin( \\theta + \\omega \\Delta t ) - \\sin\\theta \\right\\} + \\nu\\omega^{-1}\\Delta t \\cos( \\theta + \\omega \\Delta t )  \\\\ \\omega^{-1}\\left\\{-\\cos( \\theta + \\omega \\Delta t ) + \\cos\\theta \\right\\} & -\\nu\\omega^{-2}\\left\\{-\\cos( \\theta + \\omega \\Delta t ) + \\cos\\theta \\right\\} + \\nu\\omega^{-1}\\Delta t\\sin( \\theta + \\omega \\Delta t ) \\\\ 0 & \\Delta t \\end{pmatrix}$</span>

これに$\boldsymbol{x} = \boldsymbol{x}\_{t-1}, \boldsymbol{u} = \boldsymbol{u}_t$を代入すると<br />$A_t$が計算できる

---

### 状態遷移モデルの近似

* 次にやること
    * $\V{x}\_t \approx \V{f}(\V{x}\_{t-1}, \V{u}\_t) + A\_t (\V{u}\_t' - \V{u}\_t)$について、<br />$\V{u}'_t$がばらつくときの$\V{x}_t$の分布を求める<br />　
* 分布の式
    * $\V{x}_t \sim \mathcal{N}(\V{x} | \V{\mu}_t , R_t)$
        * $\V{\mu}\_t = \V{f}(\V{x}\_{t-1}, \V{u}\_t)$
        * $R_t$は誤差項$A_t(\V{u}'_t - \V{u}_t)$のばらつきの共分散行列<br />　
* $R_t$の求め方
    * $\nu\omega$空間にある誤差$\V{u}'-\V{u}$の分布を$XY\theta$空間に$A_t$で写像

計算しましょう

---

### $R_t$の計算

1. 制御のばらつきを$\boldsymbol{u}' \sim \mathcal{N}(\boldsymbol{u}, M_t)$でモデル化
    * $M\_t = \dfrac{1}{\Delta t}\begin{pmatrix} \sigma^2\_{\nu\nu}|\nu\_t| + \sigma^2\_{\nu\omega}|\omega\_t| & 0 \\\\ 0 & \sigma^2\_{\omega\nu}|\nu\_t| + \sigma^2\_{\omega\omega}|\omega\_t| \end{pmatrix}$
    * $\sigma^2_{ab}$: 移動量$b$あたりの$a$の分散
    * <span style="color:red">これはパーティクルフィルタで使ったモデルと同じ</span>
    * すべて分かっている変数で構成される<br />　
2. 共分散行列の定義から
    * $R\_t = \left\langle  (\V{x}\_t - \V{\mu}\_t) (\V{x}\_t - \V{\mu}\_t)^\top \right\rangle\_{\mathcal{N}(\V{u}, M\_t)}$<br />
    $= \left\langle A\_t (\V{u}'\_t - \V{u}\_t) \left\\{ A\_t (\V{u}'\_t - \V{u}\_t) \right\\}^\top \right\rangle\_{\mathcal{N}(\V{u}, M\_t)}$
    $= A\_t  \left\\{ \left\langle (\V{u}'\_t - \V{u}\_t) (\V{u}'\_t - \V{u}\_t)^\top \right\rangle\_{\mathcal{N}(\V{u}, M\_t)} \right\\} A\_t^\top$
	<span style="color:red">$ = A\_t M\_t A\_t^\top$</span><br />
        <span style="font-size:60%">※ 書籍（付録B.1.10）はもう少し回りくどい方法で計算しています。書き直したい・・・</span>

---

## 6.2.2 信念分布の遷移

* 今まで: 姿勢の状態遷移をガウス分布で近似
    * 点を分布に
* 次やること: 信念分布の状態遷移をガウス分布で近似
    * 分布を分布に<br />　
* 手順
    1. 信念$\ b\_{t-1} = \mathcal{N}(\V{\mu}\_{t-1}, \Sigma\_{t-1} )$を$\hat{b}_t$に遷移する計算式をたてる
        * ベイズフィルタの移動の式$\ \hat{b}\_t(\V{x}) = \int\_{\V{x}' \in \mathcal{X}} p(\V{x} | \V{x}', \V{u}\_t) b\_{t-1}(\V{x}') d\V{x}'$で
        * 状態遷移モデル$p(\V{x} | \V{x}\_{t-1}, \V{u}\_t)$は今作ったガウス分布を利用
    2. $\hat{b}_t$がガウス分布になるように近似の方法を考える<br />　

---

### ガウス分布を代入した<br />ベイズフィルタの移動の式

* 代入するガウス分布
    * $p(\V{x} | \V{x}', \V{u}\_t) = \mathcal{N}(\V{x} | \V{\mu}\_t, R\_t) $
    $= \mathcal{N}\left[ \V{f}(\V{x}',\V{u}\_t),R\_t \right]$
   <span style="color:red">$= \eta \exp\left\\{ -\frac{1}{2} \left[\V{x} - \V{f}(\V{x}',\V{u}\_t) \right]^\top R\_t^{-1} \left[ \V{x} - \V{f}(\V{x}',\V{u}\_t) \right] \right\\}$</span>
        * $R_t$の逆行列は存在しないがその問題はあとで扱う
    * $b\_{t-1}(\V{x}') = \mathcal{N}( \V{x}' | \V{\mu}\_{t-1}, \Sigma\_{t-1} ) $
    <span style="color:red">$= \eta \exp\left\\{-\frac{1}{2}(\V{x}' - \V{\mu}\_{t-1})^\top \Sigma\_{t-1}^{-1} (\V{x}' - \V{\mu}\_{t-1})  \right\\}$</span><br />　
* 代入して式を整理
    * $ \hat{b}\_t(\V{x}) = \eta\int\_{\V{x}' \in \mathcal{X}} $<span style="font-size:90%">$\exp\big\\{ -\frac{1}{2} \big[\V{x} - \V{f}(\V{x}',\V{u}\_t) \big]^\top R\_t^{-1} \big[ \V{x} - \V{f}(\V{x}',\V{u}\_t) \big] \\\\ -\frac{1}{2} (\V{x}' - \V{\mu}\_{t-1})^\top \Sigma\_{t-1}^{-1} (\V{x}' - \V{\mu}\_{t-1}) \big\\}  d\V{x}' $</span>

$\V{f}$が非線形なので$\hat{b}_t$がガウス分布にならない

---

### $\V{f}$の線形化

* $\hat{b}_t$の積分内の指数部
    * <span style="font-size:75%">$-\dfrac{1}{2} \big[\V{x} - \V{f}(\V{x}',\V{u}\_t) \big]^\top R\_t^{-1} \big[ \V{x} - \V{f}(\V{x}',\V{u}\_t) \big] -\dfrac{1}{2} (\V{x}' - \V{\mu}\_{t-1})^\top \Sigma\_{t-1}^{-1} (\V{x}' - \V{\mu}\_{t-1}) $</span>
    * <span style="color:red">$\V{x}'$を非線形な関数$\V{f}$の外に出すと、指数部が$\V{x}'$の分布に対応する多項式と$\V{x}$の分布に対応する多項式に分けることが可能</span>
        * この分離で$\hat{b}_t$がガウス分布に（理由はあとで）<br />　
* 今度は$\V{x}'$（$= \V{x}_{t-1}$）に着目して$\V{f}$の線形近似を行う
    * $ \V{f}(\V{x}', \V{u}\_t) \approx \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) + F\_t(\V{x}' - \V{\mu}\_{t-1}) $
        * $F\_t = \dfrac{\partial \V{f}(\V{x}', \V{u})}{\partial \V{x}'}\Big|\_{ \V{x}' = \V{\mu}\_{t-1}}$
            * さっきは$\V{u}$で偏微分していた<br />　
* 近似式の意味
    * 移動前の姿勢$\V{x}'$が信念分布の中心$\V{\mu}\_{t-1}$から$\V{x}'-\V{\mu}\_{t-1}$だけずれているとき、その誤差は移動後に$F_t$倍拡大される

---

### $F_t$の計算

* <span style="font-size:70%">$F\_t = \\begin{pmatrix} \\partial f\_x(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial x\_{t-1} & \\partial f\_x(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial y\_{t-1} & \\partial f\_x(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial \\theta\_{t-1} \\\\ \\partial f\_y(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial x\_{t-1} & \\partial f\_y(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial y\_{t-1} & \\partial f\_y(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial \\theta\_{t-1} \\\\ \\partial f\_\\theta(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial x\_{t-1} & \\partial f\_\\theta(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial y\_{t-1} & \\partial f\_\\theta(\\boldsymbol{x}\_{t-1}, \\boldsymbol{u}) / \\partial \\theta\_{t-1} \\end{pmatrix} \\Bigg|\_{ \\boldsymbol{x}\_{t-1} = \\boldsymbol{\\mu}\_{t-1}}$</span><br />　
<span style="font-size:70%">$= \\begin{pmatrix} 1 & 0 & \\nu\_t\\omega\_t^{-1}\\{\\cos(\\theta\_{t-1} + \\omega\_t\\Delta t) - \\cos \\theta\_{t-1} \\} \\\\ 0 & 1 & \\nu\_t\\omega\_t^{-1}\\{\\sin(\\theta\_{t-1} + \\omega\\Delta t) - \\sin \\theta\_{t-1} \\} \\\\ 0 & 0 & 1 \\end{pmatrix} \\Bigg|\_{ \\boldsymbol{x}\_{t-1} = \\boldsymbol{\\mu}\_{t-1}}$</span><br />　
<span style="font-size:70%">$= \\begin{pmatrix} 1 & 0 & \\nu\_t\\omega\_t^{-1}\\{\\cos(\\mu\_{\\theta\_{t-1}} + \\omega\_t\\Delta t) - \\cos \\mu\_{\\theta\_{t-1}} \\} \\\\ 0 & 1 & \\nu\_t\\omega\_t^{-1}\\{\\sin(\\mu\_{\\theta\_{t-1}} + \\omega\_t\\Delta t) - \\sin \\mu\_{\\theta\_{t-1}} \\} \\\\ 0 & 0 & 1 \\end{pmatrix}$</span>




---

### 指数部の計算

* 指数部に近似式を代入
    * <span style="font-size:90%">$ = - \dfrac{1}{2}\left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) - F\_t(\V{x}' - \V{\mu}\_{t-1}) \right]^\top R\_t^{-1} \left[ \text{省略} \right] \\\\ - \dfrac{1}{2}( \V{x}' - \V{\mu}\_{t-1} )^\top \Sigma\_{t-1}^{-1} ( \text{省略} ) $</span><br />
<span style="font-size:90%">$ = - \dfrac{1}{2}\left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right]^\top (F\_t \Sigma\_{t-1} F\_t^\top + R\_t)^{-1} \left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right] $</span><br />
<span style="font-size:90%">$- \dfrac{1}{2} (\V{x}'$の二次形式$)+$定数</span><br />
        * 二次形式: ガウス分布の指数部が作れるということ

それぞれの項を$L(\V{x}), L'(\V{x}'), C$と表して<br />元の式に戻しましょう

---

### $\hat{b}_t$はガウス分布になる

* $ \hat{b}\_t(\V{x}) = \eta\int\_{\V{x}' \in \mathcal{X}} \exp\big\\{$指数部$ \big\\}  d\V{x}'$<br />
$ = \eta\int\_{\V{x}' \in \mathcal{X}} \exp\\{L(\V{x}) \\}\exp\\{L'(\V{x}') \\} \exp ( C ) d\V{x}'$
$ = \eta \exp(C) \exp\\{L(\V{x}) \\}\int\_{\V{x}' \in \mathcal{X}} \exp\\{L'(\V{x}') \\} d\V{x}'$
<br /><span style="font-size:70%">$\qquad\qquad$（↑$C, L$は$\V{x}'$を含まないので）</span><br />
$ = \eta \exp\\{L(\V{x}) \\}\int\_{\V{x}' \in \mathcal{X}} \mathcal{N}(\V{x}') d\V{x}'$
<br /><span style="font-size:70%">$\qquad\qquad$（↑定数を整理、$L'$で$\V{x}'$のガウス分布を作る）</span><br />
$ = \eta \exp\\{L(\V{x}) \\}$<br />
$ = \eta e^{-\frac{1}{2}\left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right]^\top (F\_t \Sigma\_{t-1} F\_t^\top + R\_t)^{-1} \left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right] }$<br />
<span style="color:red">$ = \mathcal{N}\left[\V{x} \Big| \V{f}(\V{\mu}\_{t-1}, \V{u}\_t), F\_t \Sigma\_{t-1} F\_t^\top + R\_t\right]$</span>

---

## 6.2.3 移動後の更新の実装

* 得られた$\hat{b}_t$の式
    * $ \hat{b}\_t(\V{x}) = \eta e^{-\frac{1}{2}\left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right]^\top (F\_t \Sigma\_{t-1} F\_t^\top + R\_t)^{-1} \left[ \V{x} - \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) \right] }$
* カルマンフィルタで実装すべき更新式
    * <span style="color:red">$\hat{\mu}\_t = \V{f}(\V{\mu}\_{t-1}, \V{u}\_t) $</span>
        * 移動前の信念分布の中心$\V{\mu}_{t-1}$を$\V{f}$で移動
    * <span style="color:red">$\hat{\Sigma}\_t = F\_t \Sigma\_{t-1} F\_t^\top + R\_t = F\_t \Sigma\_{t-1} F\_t^\top + A\_t M\_t A\_t^\top$</span>
        * 移動前の共分散行列$\Sigma_{t-1}$を移動による誤差の拡大行列$F_t$で大きくして、移動による雑音の共分散行列$R_t$を足す

導出がややこしい割にはたったこれだけ

---

### 実装

* 左図: 3台のロボットを動かした結果
* 右図: 左回りで100台のロボットを30[s]動かした結果
    * 左図で左回りをしているロボットの位置推定結果と比較すると、分布の形状が単純化されているのが分かる

<img width="40%" src="figs/kalman_no_obs.gif" />
<img width="40%" src="figs/particles_vs_robots_robots.png" />

---

### $R_t$に逆行列がない問題

* $ R\_t = A\_t M\_t A\_t^\top$
    * $R_t$は$3\times 3$行列だが$M_t$は$2\times 2$行列なのでランク落ち<br />$\Longrightarrow$逆行列は存在しない
    * 物理的な意味
        * 速度、角速度の2変数に雑音をのせても<br />$XY\theta$の3次元では雑音が3次元に広がらない<br />　
* 困るのかどうか$\Longrightarrow$困らない
    * 実用上の理由: $\hat{\Sigma}\_t = F\_t \Sigma\_{t-1} F\_t^\top + R\_t$に$R\_t^{-1}$が登場しない
    * 物理的な理由: 速度・角速度の雑音がなくても不確かさ$F\_t \Sigma\_{t-1} F\_t^\top$は存在<br />
（本来は数式で証明しなければならない）

