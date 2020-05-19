$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 7. 自己位置推定の<br />諸問題

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 本章でやること

* パーティクルフィルタを用いた自己位置推定（MCL）<br />について
    * パーティクルの個数を可変に（7.1節）
    * スタックや誘拐問題への対応（7.2節以降）

---

## 7.1 KLDサンプリング

* こういうことはできないか
    * 推定姿勢が不確かなときはパーティクルの数を多く
        * 近似精度を確保
    * 推定姿勢が確かなときはパーティクルの数を少なく
        * 計算量を小さく

「KLDサンプリング」という手法を用いて実現

---

## 7.1.1 パーティクル数の決定問題

* ある信念分布をある正確さで近似するために必要な<br />パーティクル数$N$は？
    * この問題を考えるためには「正確さ」の数値化が必要<br />　
* <span style="color:red">カルバック・ライブラー情報量</span>で数値化
    * Kullback-Leibler divergence, KL divergence, KL情報量
    * 次の式で二つの分布$P,Q$の「近さ」を比較
        * $D\_\\text{KL}(P||Q) = \\sum_{s \\in \\mathcal{S}} P(s)\\log \\dfrac{P(s)}{Q(s)} = \\left\\langle \\log P(s) \\right\\rangle\_{P(s)} - \\left\\langle \\log Q(s) \\right\\rangle\_{P(s)}$
            * $\mathcal{S}$は有限個の要素の集合
    * 性質
        * $P=Q \Longrightarrow D_\text{KL} = 0$
        * $P\neq Q \Longrightarrow D_\text{KL} > 0$


---

### KL情報量を用いた$N$の算出

* 真の信念分布$b^*$、パーティクルの示す信念分布$b$間のKL情報量を求める

