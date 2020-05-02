$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 3. 自律ロボットの<br />モデル化

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 本章の内容

* 以後の章で使うロボットのシミュレータを作る
    * 作るもの
        * ロボットの動作
        * ロボットのセンシング
    * 雑音は考慮しない<br />　
* ロボットの系を定式化する
    * 状態方程式
    * 観測方程式

---

## 3.1 想定するロボット

* 対向2輪型ロボット
    * 車軸の両側に駆動輪がついており、<br />軸の中心まわりに（つまりその場で）回転可能
    * 本書では平面上を動くことを仮定
        * 実際にはスロープを登り降りできるが、扱わない

<iframe width="300" height="280" src="https://www.youtube.com/embed/zm0gP6o09lM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<img width="20%" src="./figs/tsukuba.jpg" />

---

### 対向2輪ロボットの動き

* 速度と角速度だけで表現できると仮定
    * 加速度は無視

<img width="70%" src="./figs/robot_vels.jpg" />


---

## 3.2 ロボットの動き

