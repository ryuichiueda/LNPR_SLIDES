$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 9. グラフ表現によるSLAM（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 9.3 移動エッジの追加

* やること
    * 移動エッジの情報を$\Omega$、$\V{\xi}$に追加
         * 手順は仮想移動エッジとほぼ同じ
         * ノードをずらしたときのマハラノビス距離の変化（精度行列）を計算し、変数$\V{x}\_{0:T}$で表された最適化の式を線形近似して$\Delta\V{x}\_{[0:T]}$の二次式に

---

## 9.3.1 移動エッジと残差

* 移動エッジの残差、残差関数
    * $\hat{\boldsymbol{e}}\_{t\_1,t\_2} = \hat{\boldsymbol{x}}\_{t\_2} - \boldsymbol{f}(\hat{\boldsymbol{x}}\_{t\_1}, \boldsymbol{u}\_{t\_2})$
    * 下図: ノード、状態遷移関数、残差、残差の精度行列の関係<br />
<img width="30%" src="./figs/9.9.jpg" /><br />
        * $\Omega_{t_1,t_2}$は状態遷移で期待される雑音の分布から作成
* 残差関数
    * $\boldsymbol{e}\_{t\_1,t\_2}(\Delta \boldsymbol{x}\_{t\_1}, \Delta\boldsymbol{x}\_{t\_2}) = \hat{\boldsymbol{x}}\_{t\_2} + \Delta\boldsymbol{x}\_{t\_2} - \boldsymbol{f}(\hat{\boldsymbol{x}}\_{t\_1} + \Delta\boldsymbol{x}\_{t\_1}, \boldsymbol{u}\_{t\_2})$
        * 最適化の式を線形化するときに、この式を線形化

---

## 9.3.2 ログの追加

* 作業 
    * 今度は観測のない姿勢も扱えるのでログに記録
    * 制御指令、制御の周期も利用するので記録


---

## 9.3.3 残差の確率モデルの構築

* カルマンフィルタのときと同じ
    * $\V{u}\_{t\_2}$で移動したときの移動後の姿勢の不確かさの共分散行列: 
    $R\_{t\_1,t\_2} = A\_{t\_2}M\_{t\_2}A\_{t\_2}^\top$
        * $M\_{t\_2}$: $\nu\omega$空間での動きの不確かさの共分散行列
        * $A\_{t\_2}$: $M\_{t\_2}$を$XY\theta$空間に写像するためのヤコビ行列
            * $\V{u}\_{t\_2}$まわりで偏微分<br />　
* 異なる点
    * 情報行列$\Omega\_{t\_1,t\_2} = R\_{t\_1,t\_2}^{-1}$を作らないといけないが、$R\_{t\_1,t\_2}$に逆行列がない
        * 共分散行列の対角成分を少し水増し
            * $R\_{t\_1,t\_2} = A\_{t\_2}M\_{t\_2}A\_{t\_2}^\top + \zeta I$
            * 少し情報を捨てることになる

---

## 9.3.4 グラフの精度行列と係数ベクトルに足す値の計算

* 評価関数$J\_\textbf{x}(\V{x}\_{0:T})$を線形化して$\Delta\V{x}\_{[0:t]}$の多項式に
    * $J\_\textbf{x}(\V{x}\_{0:T}) =  \sum\_{\textbf{e}\_\textbf{x}} \left\\{\V{e}\_{t\_1,t\_2}(\V{x}\_{t\_1},\V{x}\_{t\_2})\right\\}^\top \Omega\_{t\_1,t\_2} \left\\{ \V{e}\_{t\_1,t\_2}(\V{x}\_{t\_1},\V{x}\_{t\_2})\right\\}$<br />　
* 残差関数の線形化
    * $\V{e}\_{t\_1,t\_2}(\Delta \V{x}\_{t\_1},\Delta \V{x}\_{t\_2}) \approx \hat{\V{x}}\_{t\_2} - \V{f}(\hat{\V{x}}\_{t\_1}, \V{u}\_{t\_2}) + \Delta \V{x}\_{t\_2} - F\_{t\_1,t\_2} \Delta \V{x}\_{t\_1}$
        * $F\_{t\_1,t\_2} = \frac{\partial \V{f}}{\partial \V{x}\_{t\_1}}\Big|\_{\V{x}\_{t\_1} = \hat{\V{x}}\_{t\_1}}$<br />　
* 変数を$\Delta\V{x}_{[0:T]}$にすると
    * <span style="font-size:55%">$\Omega^\*\_{\boldsymbol{x}\_{t\_1},\boldsymbol{x}\_{t\_2}} = \begin{pmatrix} \ddots &   &  &  \\\\ & F\_{t\_1,t\_2}^\top\Omega\_{t\_1,t\_2}F\_{t\_1,t\_2} & -F\_{t\_1,t\_2}^\top\Omega\_{t\_1,t\_2} &  \\\\ & -\Omega\_{t\_1,t\_2}F\_{t\_1,t\_2} &  \Omega\_{t\_1,t\_2} &  \\\\ & & & \ddots \end{pmatrix}$、$\xi^*\_{\boldsymbol{x}\_{t\_1},\boldsymbol{x}\_{t\_2}} = - \begin{pmatrix} \vdots \\\\ -F\_{t\_1,t\_2}^\top \\\\ I \\\\ \vdots \end{pmatrix} \Omega\_{t\_1,t\_2} \left\\{ \hat{\boldsymbol{x}}\_{t\_2} - \boldsymbol{f}(\hat{\boldsymbol{x}}\_{t\_1}, \boldsymbol{u}\_{t\_2}) \right\\}$</span>
