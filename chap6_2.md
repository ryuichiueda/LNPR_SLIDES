$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 6. カルマンフィルタによる自己位置推定（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 6.3 観測後の信念分布の更新

* やること
    * 観測後の信念がガウス分布になるように近似方法を考えて実装

---

## 6.3.1 近似前の更新式

* ひとつのセンサ値$\V{z}$を信念分布に反映する式
    * $b(\V{x}) = \eta p(\V{z} | \V{x}) \hat{b}(\V{x}) = \eta L(\V{x} | \V{z}) \hat{b}(\V{x})$
        * ランドマーク1個、時間の流れも気にしなくていいので添字は省略<br />　
* 尤度関数はパーティクルフィルタのものと共通
    * $L(\V{x} | \V{z}) = \mathcal{N}\left[ \V{z} | \V{h}(\V{x}), Q(\V{x}) \right]$
        * $Q(\V{x}) = \begin{pmatrix} [\ell(\V{x})\sigma_\ell]^2 & 0 \\\\ 0 & \sigma^2_\varphi \end{pmatrix}$
        * $\ell(\V{x})$: $\V{x}$とランドマーク$\text{m}$の距離
        * $\V{h}$: 観測関数

$b$の式を具体的に書いてみましょう


---

### $b_t$の式

* 尤度関数と信念$\hat{b}$の掛け算になる
    * $b(\V{x}) = \eta \exp \left\\{ -\frac{1}{2} \left[ \V{z} - \V{h}(\V{x}) \right]^\top Q\_{\V{x}}^{-1} \left[ \V{z} - \V{h}(\V{x}) \right] \\\\ \qquad\qquad\qquad -\frac{1}{2} ( \V{x} - \hat{\V{\mu}} )^\top \hat{\Sigma}^{-1} ( \V{x} - \hat{\V{\mu}} ) \right\\}$
        * $Q_{\V{x}}$: $Q(\V{x})$のこと
        * $\hat{b} = \mathcal{N}(\hat{\V{\mu}}, \hat\Sigma)$
        * $\V{h}(\V{x}) = \begin{pmatrix} \sqrt{(x - m_x)^2 + (y - m_y)^2} \\\\ \text{atan2}(m_y - y, m_x - x) - \theta \end{pmatrix}$<br />　
* ガウス分布になっていない
    * 尤度関数が$\V{z}$のガウス分布であって$\V{x}$のガウス分布ではない
    * $\V{h}$が非線形
