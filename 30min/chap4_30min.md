## 4. 不確かさのモデル化

千葉工業大学 上田 隆一
2019年4月24日

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 実世界の不確かさ（センサ）

* 例
    * 同じものを計測しても毎回違う値（偶然雑音）
    * 周囲に観測するものがない
    * 定常的に計測値がずれる（系統誤差・バイアス）
    * 壁だと思ったら人までの距離を計測（過失誤差）

<div style="float:left">
<img width="40%" src="../figs/sensor_200_histgram.png" />
</div>
<div style="float:left">
<iframe width="560" height="315" src="https://www.youtube.com/embed/RpPcmyXOcr4?start=2444" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

---

### 実世界の不確かさ（動き）

* 例
    * 小石・縁石を踏んだ、人に持ち上げられた
        * 前のスライドのムービー
    * モータへの指令値と実際の出力が少しずれている
        * 「ロボットがまっすぐ走らないんですよ」（当たり前）

<iframe width="260" height="315" src="https://www.youtube.com/embed/wNm9dhWBqZM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

### 雑音のシミュレーション

---

### 路面からの雑音のシミュレーション

* モデル: ある一定の<span style="color:red">確率</span>で小石を踏む
    * 小石は穴でも岩でもなんでもよい
    * 以下のパラメータで頻度と深刻度が決まる
        * どれだけの確率で起こるか
        * 踏んだらどれだけロボットがずれるか

---

### 出力のバイアス

指令値$(\nu \ \omega)^T$に同じだけ誤差を
