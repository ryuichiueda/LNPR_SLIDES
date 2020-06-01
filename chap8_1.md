$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 8. パーティクル<br />フィルタによるSLAM（前半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 本章でやること

* SLAMの問題を理解
* FastSLAMの導出と実装

---

### SLAM

* simultaneous localization and mapping
    * 自己位置推定と地図生成を同時に実行すること<br />　
* ランドマークの位置が分からないのに自己位置推定できるのか？
    * 素直に次のようにすればできる
        1. 白地図を用意
        2. ロボットの初期姿勢を原点にして世界座標系を設定
        3. 移動して姿勢を更新し、ロボットが観測したものを世界座標系の位置に変換して白地図に書き込むことを繰り返す

<span style="color:red">ただし雑音が厄介</span>

---

## 8.1 逐次SLAMの解き方
### 8.1.1 SLAMの問題と部分問題への分解

* SLAM問題: $b_t(\V{x}\_{1:t}, \textbf{m}) = p(\V{x}\_{1:t}, \textbf{m} | \V{x}\_0, \V{u}\_{1:t}, \textbf{z}\_{1:t})$を計算
   * ここで
       * $\V{x}\_{1:t}$: ロボットの姿勢の履歴（軌跡）
       * $\textbf{m}$: 地図（全ランドマークの位置）
           * $\textbf{m} = \\{\V{m}\_0, \V{m}\_1, \dots, \V{m}\_{N\_\textbf{m}}\\}$
   * 用途
       * 地図が未知の自己位置推定
       * 地図と履歴を利用（$b_t$を最大化する$\V{x}\_{1:t}, \textbf{m}$だけ求める）
       * 地図を利用（$b_t$を最大化する$\textbf{m}$だけ求める）


いずれにせよ非常に多次元の分布を求めることに


---

### <span style="text-transform:none">Rao-Blackwellization</span>

* 分布が多次元すぎて扱えないので変形
    * 軌跡の分布と地図の分布に分解できる<br />（ラオ・ブラックウェル化）
<span style="font-size:80%">$$\begin{align} &b\_t(\boldsymbol{x}\_\{1:t\}, \textbf{m}) = p(\boldsymbol{x}\_\{1:t\},\textbf{m} | \boldsymbol{x}\_{0}, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) \\\\
&=
p(\textbf{m} | \boldsymbol{x}\_\{1:t\}, \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) & （乗法定理）\\\\
&=
p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
p(\textbf{m} | \boldsymbol{x}\_{0:t}, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) & （左右入れ替え）\\\\
&=
p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
p(\textbf{m} | \boldsymbol{x}\_{0:t}, \textbf{z}\_\{1:t\}) & （不要な条件を削除）\\\\
\end{align}$$</span>
    * 左の分布の軌跡をパーティクルで表す$\Longrightarrow$<br />パーティクルごとに地図を推定する問題に分解される
        * <span style="color:red">Rao-Blackwellized particle filter（RBPF）</span><br />という種類のパーティクルフィルタに
