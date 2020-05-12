$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 6. カルマンフィルタによる自己位置推定

千葉工業大学 上田 隆一

<br />

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


---

## 6.1 信念分布の近似と描画

* 信念をガウス分布として実装
    * $b_t = \mathcal{N}(\V{\mu}_t, \Sigma_t)$<br />　
* 誤差楕円を描く
    * 2章で扱いました
    * シミュレータでは次の2要素で描画
        * $XY$平面の$3\sigma$範囲を表す楕円
        * $\theta$方向の$3\sigma$範囲を示す2本の線分
    * 本来は$XY\theta$空間の楕円球
        * （ユークリッド空間とみなせば）

<img width="40%" src="./figs/belief_ellipse.png" />
