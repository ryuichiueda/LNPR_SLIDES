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

### 真の信念分布とパーティクルの分布の比較

* 真の信念分布$b^*$と、パーティクルによる近似$\hat{b}$を想定
    * $b^*$が不明なのでいずれも想定上のもの<br />　
* 次のようなヒストグラム状の確率分布$\hat{P}$を考える
    * $XY\theta$空間を格子状に区切って離散化
        * 区画の大きさ: 後の実験では200[mm]$\times$200[mm]$\times$10[deg]
    * 区画一つ一つに$s_0, s_1, s_2, \dots$と番号づけ
    * 各区画に入っている$\hat{b}$のパーティクルの個数の和: $\hat{P}$<br />　
* 同様に$b^\*$から確率分布$P^\*$を考え、<br />$P^\*$と$\hat{P}$を$D_\text{KL}(\hat{P} || P^\*)$で比較

---

### 近似精度の目標の設定

* まず、$D_\text{KL}(\hat{P} || P^*) \le\varepsilon$という目標（目標1）を考える
    * $b^*$を$\hat{b}$で近似したとき、KL情報量が、設定した閾値$\varepsilon$を下回る
    * 必要なパーティクルの数$N$は？<br />　
* 目標1はどんなに$N$が大きくても達成できない可能性
    * $\hat{b}$の分布が偏ると$D_\text{KL}(\hat{P} || P^\*)$が小さく
* そこで、目標2: $$P\left[ D_\text{KL}(\hat{P} || P^*) \le\varepsilon \right] \ge 1 - \delta$$
を設定
    * 目標1が達成される確率が、閾値$\delta$を設定したときに$1-\delta$以上

これで$N$が決まるが、$b^*$が不明という問題が残る

---

## 7.1.2 対数尤度比の性質による<br />パーティクル数の決定

* 次にやること: 
    * $D_\text{KL}(\hat{P} || P^\*)$がどのようにばらつくかを（間接的に）調べる<br />　
* 手順
    * $b^*$から$N$個のパーティクルをドローしたとき、<br />
    離散区間$s_i$（ビンと呼ぶ）に、$n_i$個入る確率の式を立てる
        * ビン = 瓶
        * $P^\*\_\text{M}(n\_0, n\_1, \dots, n\_{k-1} | p^\*\_0, p^\*\_1, \dots, p^\*\_{k-1}) \\\\ = \dfrac{N!}{n\_0! n\_1!\dots n\_{k-1}!} (p^\*\_0)^{n\_0} (p^\*\_1)^{n\_1} \dots (p^\*\_{k-1})^{n\_{k-1}} \\ = \dfrac{N!}{\prod\_{j=0}^{k-1} (n\_j !) }\prod\_{j=0}^{k-1} (p^\*\_j)^{n\_j}$

