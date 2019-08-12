## 12. 部分観測マルコフ決定過程

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 12.1 部分観測マルコフ決定過程（POMDP）

---

### POMDPの問題

* 問題はMDPだがエージェントが真の状態を把握できない
    * 自己位置推定などで自身で観測しなければならない
* POMDPにおける方策: $a\_{t+1} = \Pi\_\text{POMDP}(a\_{1:t}, \textbf{z}\_{1:t}, r\_{1:t})$
    * 状態がわからないのでこれまでに知ったことから行動を判断
    * この形式のままだと実装方法は見えない

---

### 自己位置推定が不確かな場合に<br />起こる問題

* 分布が広い時に分布の端にロボットがいると

<img src="../figs/uncertain_navigation_1.png" />
