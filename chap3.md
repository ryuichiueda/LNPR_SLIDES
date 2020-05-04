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
    * トルクや加速度は無視

<img width="70%" src="./figs/robot_vels.jpg" />

これをシミュレータ上に再現

---

## 3.2 ロボットの動き

---

## 3.2.1 世界座標系と描画

* ロボットの動き回る平面を準備
    * 図のように$X$軸、$Y$軸を設置
    * <span style="color:red">世界座標系</span>$\Sigma_\text{world}$と名付ける
        * 複数の座標系の関係としてロボットの動きを考えることはロボット工学では極めて重要ですが、書籍では世界座標系しか出てきません。

<img width="30%" src="./figs/world.png" />

---

## 3.2.2 ロボットの姿勢と描画

* $\Sigma_\text{world}$の上にロボットを置く<br />　
* ロボットが$\Sigma_\text{world}$で<br />
どのように存在しているか
    * $(x \ y)^\top$: 位置、$\theta$: 向きで表せる
    * 「<span style="color:red">姿勢</span>」と呼ぶ<br />　
* 加速度を考えていないので、<br />この3変数だけ考えると制御可能
    * 制御工学の用語で「<span style="color:red">状態</span>」とも呼べる<br />　
* ベクトル $\V{x} = (x \ y \ \theta)^\top$として表現


<img width="30%" src="./figs/robot_pose.png" />

---

### 状態と状態空間

制御の話をするために用語を整理

* ロボットのとりうる状態$\V{x}$の集合を$\mathcal{X}$とする
    * $\mathcal{X}$を<span style="color:red">状態空間</span>と呼ぶ
    * 要はロボットが行ける範囲<br />　
* 数式での表現
    * <span style="font-size:90%">$\mathcal{X} = \\{ \V{x} = (x \ y \ \theta)^\top | x \in [x_\text{min},x_\text{max}] ,y \in [y_\text{min},y_\text{max}], \theta \in [-\pi, \pi) \\}$</span>
    * $\V{x} \in \mathcal{X}$

簡単なうちに集合の表現をおさえておきましょう

---

## 3.2.3 アニメーションの導入

* 作業の話はスライドでは割愛<br />　
* 時刻を離散的に表現
    * 1ステップの時間を$\Delta t$とする
    * $\Delta t$ごとに、時刻に$t=0,1,2,\dots$と番号を付与<br />　
* 以後は離散時間でロボットの動きを考える
    * ロボットは連続時間の中に存在しているが、<br />基本的に周期的にしか計算ができないので

---

## 3.2.4 ロボットの運動と<br />状態方程式

* 扱う問題: ある時刻にロボットが動いたときに、<br />次のステップにロボットの姿勢がどうなるか

---

### 制御指令

* ロボットに与える速度、角速度を<br />それぞれ$\nu$[m/s]、$\omega$[rad/s]と表現
    * まとめて$\V{u} = (\nu \ \omega)^\top$と表現
    * <span style="color:red">制御指令</span>と呼ぶ
        * 制御入力などとも呼ぶが、<br />入力か出力か紛らわしいので

<img width="30%" src="./figs/control_input.jpg" />

世界座標系におけるロボットの動きは？

---

### 世界座標系におけるロボットの動き

* こうなる
    * $\dot{x} = \nu \cos \theta$　　　　
    * $\dot{y} = \nu \sin \theta$
    * $\dot{\theta} = \omega$<br />　<br />　<br />　

<img width="30%" src="./figs/robot_motion.jpg" />

時刻$t-1$から$t$の間に制御指令$\V{u}\_t$で<br >姿勢は$\V{x}\_{t-1}$からどう変わるか（計算できます？）


---

### 姿勢の変化の計算

* 向き$\theta$の変化は単純
    * $\theta_{t} = \theta_{t-1} + \int_{0}^{\Delta t} \omega_t dt  = \theta_{t-1} + \omega_t \Delta t$<br />　
* 位置の変化の計算では時間$\Delta t$内での向きの変化を考慮しなければならない
    * $\begin{pmatrix} x_t \\\\ y_t \end{pmatrix} = \begin{pmatrix} x_{t-1} \\\\ y_{t-1} \end{pmatrix} + \begin{pmatrix} \int_0^{\Delta t} \nu_t \cos ( \theta_{t-1} + \omega_t t ) dt\\\\ \int_0^{\Delta t} \nu_t \sin ( \theta_{t-1} + \omega_t t ) dt \end{pmatrix}$<br />
$= \cdots$<br />
$= \begin{pmatrix} x\_{t-1}  \\\\ y\_{t-1} \end{pmatrix} + \nu\_t\omega\_t^{-1} \begin{pmatrix} \sin( \theta\_{t-1} + \omega\_t \Delta t ) - \sin\theta\_{t-1} \\\\ -\cos( \theta\_{t-1} + \omega\_t \Delta t ) + \cos\theta\_{t-1} \end{pmatrix}$
         * $\omega = 0$の場合は別の式になるが極限をとると一致

---

### 状態方程式

* 前ページの計算結果のまとめ
    * <span style="font-size:90%">$\begin{pmatrix} x_t \\\\ y_t \\\\ \theta_t \end{pmatrix} = \begin{pmatrix} x\_{t-1}  \\\\ y\_{t-1} \\\\ \theta\_{t-1} \end{pmatrix} + \nu\_t\omega\_t^{-1} \begin{pmatrix} \sin( \theta\_{t-1} + \omega\_t \Delta t ) - \sin\theta\_{t-1} \\\\ -\cos( \theta\_{t-1} + \omega\_t \Delta t ) + \cos\theta\_{t-1} \\\\ \omega_t \Delta t\end{pmatrix}$</span><br />　
* 面倒なので次のように書く
    * $\V{x}\_t = \V{f}(\V{x}\_{t-1},\V{u}\_t) \qquad (t=1,2,3,\dots)$
    * ロボットの動きはこれだけで表される（雑音がなければ）<br />　
* 用語
    * 上の方程式: <span style="color:red">状態方程式</span>
    * 関数$\V{f}$: <span style="color:red">状態遷移関数</span>
    * 状態が$\V{x}\_{t-1}$から$\V{x}_t$に変わること: <span style="color:red">状態遷移</span>

---

### 状態方程式で作ったロボットの動き

<img width="40%" src="./figs/robot_motion.gif" /> 

---

## 3.2.5 エージェントの実装
## 3.2.6 離散時刻の実装

* 作業については省略<br />　
* 用語
    * エージェント: 考える主体のこと
        * 「エージェント」、「ロボット」を使い分ける場合、<br />後者はハードウェアを指す
        * <span style="color:red">制御の観点ではハードウェアも環境の一部</span>

---

## 3.3 ロボットの観測

* ロボットにセンサを搭載
    * カメラで何かの位置を計測するというモデル
        * LiDARなどの登場で古典的になってしまったが数式の理解には一番良い

---

## 3.3.1 点ランドマークの設置

* 点ランドマーク
    * カメラで観測すると、方角と距離が計測できるもの
    * 下図: 点ランドマークとみなせる物体の例

<img width="70%" src="./figs/landmarks.jpg" /> 

---

## シミュレータ中のランドマーク

* 環境中に$N_\textbf{m}$個置く<br />　
* 記号の定義
    * 一つ一つにIDを付与し、$\text{m}_0, \text{m}_1, \text{m}_2, \dots$と表す
        * 座標は$\V{m}\_j = (m\_{j,x} \ m\_{j,y})^T$
            * 普通の字体とイタリック体を使い分けるので注意
    * ランドマークの集合を<span style="color:red">地図</span>と呼ぶ 
        * 地図: $\textbf{m} = \\{ m_j | j = 0,1,2,\dots, N_\textbf{m} -1 \\}$

<img width="30%" src="./figs/landmarks.png" /> 

---

## 3.3.2 点ランドマークの観測

* シミュレータの設定
    * センサから見て距離$\ell$と方角$\varphi$が計測可能
        * これらの値をセンサ値 $\V{z} = (\ell \ \varphi)^T$と呼ぶ
        * センサ座標系とロボット座標系は同じとする<br />　
* ランドマークの位置とセンサ値の関係
    * $\ell\_j = |\V{m}\_j - \V{x}| = \sqrt{(m\_{j,x} - x)^2 + (m\_{j,y} - y)^2}$
    * $\varphi_j = \text{atan2}(m_{j,y} - y, m_{j,x} - x) - \theta$

<img width="35%" src="./figs/landmark_obs.jpg" /> 


---

### 観測方程式

* 前ページの計算結果のまとめ
    * $\begin{pmatrix} \ell\_j \\\\  \varphi_j \end{pmatrix} = \begin{pmatrix} \sqrt{(m\_{j,x} - x)^2 + (m\_{j,y} - y)^2} \\\\ \text{atan2}(m_{j,y} - y, m_{j,x} - x) - \theta\end{pmatrix}$<br />　
* 面倒なので次のように書く
    * $\V{z}\_j = \V{h}_j (\V{x})$
    * $\V{z}\_j = \V{h}(\V{x}, \V{m}_j)$（ランドマークの位置を変数とする場合）
    * ロボットの観測はこれだけで表される（雑音がなければ）<br />　
* 用語
    * 上の方程式: <span style="color:red">観測方程式</span>
    * 関数$\V{h}_j$: <span style="color:red">観測関数</span>


---

### 観測方程式で作ったロボットの観測

* 左: ロボットの姿勢とランドマークの位置から<br />センサ値を描画
* 右: 計測可能な範囲に制限を加えたもの
    * 距離計測: $0.5$〜$6$[m]
    * 方角計測: $-60$〜$60$[deg]

<img width="30%" src="./figs/observation_nolimit.gif" />　　
<img width="30%" src="./figs/simulator_no_noise.gif" /> 

---

## 3.4 コードの保存と再利用

作業なので省略

---

## 3.5 まとめ

* 本章で実装した<span style="color:red">制御系</span>
    * 次の2つの式が全て
        * 状態方程式: $\V{x}\_t = \V{f}(\V{x}\_{t-1},\V{u}\_t)$
        * 観測方程式: $\V{z}_{j,t} = \V{h}_j(\V{x}_t)$<br />　
    * 非線形時不変離散時間系
        * 非線系: 行列の積と和で表現不可能
        * 時不変系: 時間で状態方程式や観測方程式が変わらない
        * 離散時間系: 時刻が離散的<br />　
* 残った作業
    * 系に不確かさがない
