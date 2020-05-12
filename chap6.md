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
* 次の手順で左辺の$\hat{b}_t$をガウス分布にする
    1. 移動モデル$p(\V{x} | \V{x}', \V{u}\_t)$をガウス分布に近似
    2. 積分した式<br />（近似しないとガウス分布にならない理由は後で説明）
	


