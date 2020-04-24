# 4. 不確かさの<br />モデル化

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 4.1 本章でやること

* シミュレータを実世界に近づける
    * トラブルのない世界<br />$\rightarrow$未来が予測可能
        * 状態方程式: $\boldsymbol{x}\_t = \boldsymbol{f}(\boldsymbol{x}\_{t-1}, \boldsymbol{u}_t)$
        * 観測方程式: $\\boldsymbol\{z\}\_j = \\boldsymbol\{h\}_j (\\boldsymbol\{x\})$（$j$: ランドマークのID）
    * 雑音や外乱を加えて未来を不確かに

<img width="35%" src="./figs/simulator_no_noise.gif" />

---

## 4.2 ロボットの移動に対する<br />不確かさの要因の実装

---

### 不確かさの要因


* とにかく様々なことが起こる
    * 小石への片輪の乗り上げ、走り出し、停止時の揺れ、縁石への乗り上げ、走行環境の傾斜、左右の車輪が同じように回らない、穴に車輪が嵌る、人が移動する、・・・

<iframe width="560" height="315" src="https://www.youtube.com/embed/Oz2wIDD02LY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

シミュレータへ実装しているときりがない

---

### 雑音や誤差の分類

* アクシデントは次の2軸で分類可能
    * どれだけ継続期間があるか
        * 例: 小石に車輪が乗り上げた（一瞬）$\leftrightarrow$縁石に乗り上げた（数秒）<br />$\leftrightarrow$右車輪の空気が少ない（ずっと継続）
    * どれだけ$\boldsymbol{x}$が動くか
        * 例: 小石に車輪が乗り上げた（小）$\leftrightarrow$人がロボットを移動（大）<br />　
* これらの分類をカバーするように本書では次の4つを実装
    * 雑音: 突発的にロボットの向きを少し変化
    * バイアス: 制御指令値と実際の出力値を常に一定量ずらす
    * スタック: ロボットを同じ姿勢に抑留
    * 誘拐: ロボットを別の場所に突然ワープ

一つずつ実装方法を見ていきましょう

