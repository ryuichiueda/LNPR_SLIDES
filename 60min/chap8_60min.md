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

* 求める信念分布: $b\_t(\boldsymbol{x}\_\{1:t\}, \textbf{m}) = p(\boldsymbol{x}_\{1:t\}, \textbf{m} | \boldsymbol{x}_0, \boldsymbol{u}_\{1:t\}, \textbf{z}_\{1:t\})$
    * $\boldsymbol{x}_\{1:t\}$: 軌跡
    * $\textbf{m}$: <span style="color:red">地図</span>
* リアルタイム/オフライン
    * オフラインのSLAM: ロボットが行動と観測を全て終わらせてから軌跡と地図を算出
    * リアルタイムのSLAM: ロボットが行動、観測するごとに軌跡と地図を算出
        * 本章はこれを扱う

---

### 軌跡の推定と地図の推定の分離

$\begin{align} &b\_t(\boldsymbol{x}\_\{1:t\}, \textbf{m}) \\\\
	&= p(\boldsymbol{x}\_\{1:t\},\textbf{m} | \boldsymbol{x}\_{0}, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\}) \\\\
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

### さらに式を分解

* 後の話のために前のページの式を各ランドマークの位置推定に分解しておく
    * $b\_t(\boldsymbol{x}\_\{1:t\}, \textbf{m}) 
  = p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t\})
  \prod\_{j=0}^{N\_\textbf{m}-1} p(\boldsymbol{m}\_j | \boldsymbol{x}\_{0:t}, \textbf{z}\_\{1:t\})$

---

### <span style="text-transform:none">FastSLAM</span>

* 次のようなパーティクルを定義して毎ステップ更新
    * $\xi_t^{(i)} = ( \boldsymbol{x}_{0:t}^{(i)}, w_t^{(i)}, \hat{\textbf{m}}_t^{(i)} )  \quad (i=0,1,2,\dots,N-1) $
* $\hat{\textbf{m}}_t^{(i)}$は地図の推定値（分布）
    * $\hat{\textbf{m}}_t^{(i)} = \\{ \hat{\boldsymbol{m}}\_\{j,t\}^{(i)}, \Sigma\_\{j,t\}^{(i)} | j=0,1,2,\dots,N\_\textbf{m}-1 \\}$
        * $\hat{\boldsymbol{m}}\_\{j,t\}^{(i)}$: IDが$j$のランドマークの推定位置
	* $\Sigma\_\{j,t\}^{(i)}$: 推定位置の共分散行列

<img width="40%" src="../figs/fastslam_particles.png" />

---

### パーティクルの性質

* 各パーティクルの軌跡$\boldsymbol{x}_{0:t}^{(i)}$は決定論的なので地図は$p(\textbf{m} | \boldsymbol{x}\_\{0:t\}, \textbf{z}\_\{1:t\})$から求められる

<img width="60%" src="../figs/fastslam_particles.png" />

---

### <span style="text-transform:none">FastSLAM</span>の演算

* A: ロボットが移動したら軌跡を更新
* センサ値が得られたら
    * B: 地図の初期化（B-1）と更新（B-2）
    * C: パーティクルの重みを更新
* これからA, B, Cと順に説明
    * 注意: これから提示する式は長い計算の結果なのでテキストを参照のこと 

---

### A: 移動時のパーティクルの処理

* この式の右辺左側の確率分布を変形してみましょう
    * $\hat{b}\_t(\boldsymbol{x}\_\{1:t\}, \textbf{m}) 
  = p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\})
  \prod\_{j=0}^{N\_\textbf{m}-1} p(\boldsymbol{m}\_j | \boldsymbol{x}\_{0:t}, \textbf{z}\_\{1:t-1\})$
    * この式は$\boldsymbol{u}_t$による移動直後の信念を表現
* 右辺左側: $p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\})$
    * 初期姿勢、制御指令の履歴、センサ値のリストの履歴から軌跡を求める問題
    * 計算は次のページ

---

### 計算
 
* 次のように状態遷移モデル$p(\boldsymbol{x}\_t | \boldsymbol{u}\_t, \boldsymbol{x}\_{t-1})$を因子として抽出できる
    * $\begin{align}
&p(\boldsymbol{x}\_\{1:t\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\})  \\\\
&=
p(\boldsymbol{x}\_\{1:t-1\}, \boldsymbol{x}\_t | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\}) \\\\
&=
p(\boldsymbol{x}\_t | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\}, \boldsymbol{x}\_\{1:t-1\})
p(\boldsymbol{x}\_\{1:t-1\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t\}, \textbf{z}\_\{1:t-1\}) \\\\
&=
p(\boldsymbol{x}\_t | \boldsymbol{u}\_t, \boldsymbol{x}\_{t-1})
p(\boldsymbol{x}\_\{1:t-1\} | \boldsymbol{x}\_0, \boldsymbol{u}\_\{1:t-1\}, \textbf{z}\_\{1:t-1\}) 
\end{align}$
* これを信念の式に戻してやると
    * $\hat{b}\_t(\boldsymbol{x}\_\{0:t\},\textbf{m}) = p(\boldsymbol{x}\_t | \boldsymbol{u}\_t, \boldsymbol{x}\_{t-1})b\_{t-1}(\boldsymbol{x}\_\{0:t-1\},\textbf{m})$
    となる
* 移動時のパーティクルの処理はMCLと同じ
    * $\boldsymbol{x}_t^{(i)} \sim p(\boldsymbol{x} | \boldsymbol{u}_t, \boldsymbol{x}_\{t-1\}^{(i)})$して、$\boldsymbol{x}_\{1:t-1\}^{(i)}$にくっつけて$\boldsymbol{x}_\{1:t\}^{(i)}$を構成
    * <span style="color:red">軌跡を推定しているが$\boldsymbol{x}_\{1:t-2\}^{(i)}$は処理に使用しない</span>

---

### B-1: センサ値による地図の初期化

* ランドマークを初めて<br />観測したときの処理
    * 位置の初期値: <span style="color:red">$\hat{\boldsymbol{m}}\_{t} = \hat{\boldsymbol{m}}$</span>
    * 共分散行列の初期値: <br /><span style="color:red">$\Sigma\_t = ( H^T Q\_{\hat{\boldsymbol{m}}}^{-1} H )^{-1}$</span>
* 記号
    * $\hat{\boldsymbol{m}} = \boldsymbol{h}(\boldsymbol{z})$
    （センサ値$\boldsymbol{z}$と<br />観測方程式$\boldsymbol{h}$
    で求めた<br />ランドマークの位置）
    * $H$は$\boldsymbol{h}$から作ったヤコビ行列
    * $Q\_{\hat{\boldsymbol{m}}\_{t}}$はセンサ値の雑音の<br />共分散行列

<img src="../figs/fastslam_init_landmarks.gif" />

---

### B-2: センサ値による地図の更新

* ランドマーク1個の推定位置の更新
    * 位置: <span style="color:red">$\hat{\boldsymbol{m}}\_t = K \left[\boldsymbol{z}\_t - \boldsymbol{h}(\hat{\boldsymbol{m}}\_{t-1}) \right] + \hat{\boldsymbol{m}}\_{t-1} $</span>
    * 共分散行列: <span style="color:red">$\Sigma_t = (I - KH ) \Sigma_{t-1}$</span>
        * ここで<span style="color:red">$K = \Sigma\_{t-1} H^T ( Q\_{\hat{\boldsymbol{m}}\_{t-1}} + H \Sigma\_{t-1} H^T )^{-1}$</span>
        * $H, Q\_{\hat{\boldsymbol{m}}\_{t-1}}$に関しては前ページ参照
        * $K$はカルマンフィルタのカルマンゲイン
* 意味
    * 位置$\hat{\boldsymbol{m}}\_t$は次の2つを足したもの
        * 前の時刻の推定値$\hat{\boldsymbol{m}}\_\{t-1\}$
        * 得られたセンサ値$\boldsymbol{z}\_t$と推定位置から得られるはずのセンサ値$\boldsymbol{h}(\hat{\boldsymbol{m}}\_{t-1})$の誤差に$K$をかけたもの
    * 共分散は$KH\Sigma_{t-1}$の分だけ小さくなる

実行例は次のページ

---

<img src="../figs/fastslam_update_landmarks.gif" />

---

### C: センサ値による重みの更新

* 観測されたランドマークの全センサ値$\boldsymbol{z}\_{j,t} \in \textbf{z}\_t$に対して
    * <span style="color:red">$w\_t^{(i)} = w\_{t-1}^{(i)} \prod\_{\boldsymbol{z}\_{j,t} \in \textbf{z}\_t} \mathcal{N}(\boldsymbol{z}\_{j,t} | \hat{\boldsymbol{z}}\_{j,t}, Q\_{\boldsymbol{z}\_{j,t}})$</span>
        * $\hat{\boldsymbol{z}}\_\{t,j\} = \boldsymbol{h}(\hat{\boldsymbol{m}}\_{j,t-1}) - H\hat{\boldsymbol{m}}\_{j,t-1} + H \hat{\boldsymbol{m}}\_{j,t-1} = \boldsymbol{h}(\hat{\boldsymbol{m}}\_{j,t-1})$
        * $Q\_{\boldsymbol{z}\_\{j,t\}} = H\Sigma\_{j,t-1} H^T + Q\_{\hat{\boldsymbol{m}}\_{j,t-1}}$
    * （長い計算の末、このような簡単な形になることに注意）

<img src="../figs/fastslam_1.0.gif" />
