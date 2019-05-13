## 8. パーティクルフィルタを<br />用いたSLAM

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### SLAM

* SLAM: simultaneous localization and mapping
    * 自己位置推定と地図生成を同時に実行すること
* ランドマークの位置が分からないのに自己位置推定できるのか？
    * 素直に次のようにすればできる
        1. 白地図を用意
        2. ロボットの初期姿勢を原点にして世界座標系を設定
        3. 移動して姿勢を更新し、ロボットが観測したものを世界座標系の位置に変換して白地図に書き込むことを繰り返す

<span style="color:red">ただし雑音が厄介</span>

---

### SLAMの問題

* 求める分布: $p(\boldsymbol{x}_\{1:t\}, \textbf{m} | \boldsymbol{x}_0, \boldsymbol{u}_\{1:t\}, \textbf{z}_\{1:t\})$
    * $\boldsymbol{x}_\{1:t\}$: 軌跡
    * $\textbf{m}$: <span style="color:red">地図</span>
* リアルタイム/オフライン
    * オフラインのSLAM: ロボットが行動と観測を全て終わらせてから軌跡と地図を算出
    * リアルタイムのSLAM: ロボットが行動、観測するごとに軌跡と地図を算出
        * 本章はこれを扱う

---

### 軌跡の推定と地図の推定の分離

$\begin{align}
	& p(\boldsymbol{x}\_\{1:t\},\textbf{m} | \boldsymbol{x}\_{0}, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) \\\\
        &=
        p(\textbf{m} | \boldsymbol{x}\_\{1:t\}, \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
        p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) \\\\
        &=
        p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
        p(\textbf{m} | \boldsymbol{x}\_{0:t}, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
        \\\\
        &=
        p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
        p(\textbf{m} | \boldsymbol{x}\_{0:t}, \textbf{z}\_\{1:t\})
\end{align}$

* 軌跡を$\boldsymbol{x}\_\{1:t\}$を一つ定めてやると、地図の推定が簡単に
    * どうやって軌跡を定めるか$\longrightarrow$<span style="color:red">パーティクルフィルタ</span>

---

### <span style="text-transform:none">FastSLAM</span>

* 次のようなパーティクルを定義
    * $\xi_t^{(i)} = ( \boldsymbol{x}_{0:t}^{(i)}, w_t^{(i)}, \hat{\textbf{m}}_t^{(i)} )  \quad (i=0,1,2,\dots,N-1) $
* $\hat{\textbf{m}}_t^{(i)}$は地図の推定値（分布）
    * $\hat{\textbf{m}}_t^{(i)} = \\{ \hat{\boldsymbol{m}}\_\{j,t\}^{(i)}, \Sigma\_\{j,t\}^{(i)} | j=0,1,2,\dots,N\_\textbf{m}-1 \\}$
        * $\hat{\boldsymbol{m}}\_\{j,t\}^{(i)}$: ランドマーク（ID$j$）の推定位置
	* $\Sigma\_\{j,t\}^{(i)}$: 推定位置の共分散行列
* 各パーティクルの軌跡$\boldsymbol{x}_{0:t}^{(i)}$は決定論的なので地図は$p(\textbf{m} | \boldsymbol{x}\_\{0:t\}, \textbf{z}\_\{1:t\})$から求められる
    * <span style="color:red">課題: どうやって各パーティクルの軌跡$\boldsymbol{x}_{0:t}^{(i)}$を決めるのか？</span>

---

### 軌跡の推定と地図の推定の分離
