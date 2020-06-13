$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 8. パーティクル<br />フィルタによるSLAM（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 8.6 <span style="text-transform:none">FastSLAM 2.0</span>

* いままでのFastSLAMの実装を工夫したバージョン
    * いままでのFastSLAMはFastSLAM 1.0と呼ばれる<br />　
* 工夫: パーティクルを状態遷移させる際、<br />センサ値も考慮
    * FastSLAM 1.0: そのまま$\V{x}\_t^{(i)} \sim p(\V{x} | \V{x}\_{t-1}^{(i)},\V{u}_t)$で遷移
    * FastSLAM 2.0: <span style="color:red">その先のセンサ値のリスト$\textbf{z}_t$まで考慮</span>

なぜそうするのか？<br />
<span style="font-size:50%">とりあえず$\textbf{z}\_t$にセンサ値$\V{z}\_{j,t}$が1個だけ含まれるという仮定で説明します。</span>

---

### センサ値まで考慮する効果

* より狭い分布<span style="font-size:90%">$p(\V{x} | \V{x}\_{t-1}^{(i)}, \hat{\V{m}}\_{j,t-1} \V{u}\_t, \V{z}\_{j,t})$</span>からドロー可能<br />
    * パーティクルが広く薄く分布されることを防ぐ
* ただし
    * どうやってこの分布を作るのか
    * 重みの計算方法が変わってしまう

<img width="50%" src="figs/8.6.jpg" />

---

## 8.6.1 センサ値を考慮したパーティクルの姿勢の更新

* パーティクルの移動に使う分布を導出
    * <span style="font-size:70%">$p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \hat{\textbf{m}}^{(i)}\_{t-1}, \V{u}\_t, \V{z}\_{j,t})$<br />
$ = \eta p( \V{z}\_{j,t} | \V{x}\_t, \V{x}^{(i)}\_{t-1}, \hat{\textbf{m}}^{(i)}\_{t-1}, \V{u}\_t) p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \hat{\textbf{m}}^{(i)}\_{t-1}, \V{u}\_t)$ <br />
$ = \eta p( \V{z}\_{j,t} | \V{x}\_t, \hat{\textbf{m}}^{(i)}\_{t-1}) p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t) $<br />
$= \eta [\\![ p( \V{z}\_{j,t}, \V{m}\_j |\V{x}\_t, \hat{\V{m}}^{(i)}\_{j,t-1}, \Sigma\_{j,t-1}^{(i)}) ]\\!]\_{\V{m}\_j} \ p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t)$<br />
$= \eta [\\![ p( \V{z}\_{j,t}, |\V{x}\_t, \V{m}\_j, \hat{\V{m}}^{(i)}\_{j,t-1}, \Sigma\_{j,t-1}^{(i)}) p(\V{m}\_j | \V{x}\_t, \hat{\V{m}}^{(i)}\_{j,t-1}, \Sigma\_{j,t-1}^{(i)}) ]\\!]\_{\V{m}\_j} \ p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t)$<br />
$= \eta [\\![ p( \V{z}\_{j,t} | \V{x}\_t, \V{m}\_j) \mathcal{N}(\V{m}\_j | \hat{\V{m}}^{(i)}\_{j,t-1}, \Sigma\_{j,t-1}^{(i)}) ]\\!]\_{\V{m}\_j} p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t) $<br />
$= \eta \big\langle p( \V{z}\_{j,t} | \V{x}\_t, \V{m}\_j) \big\rangle\_{\mathcal{N}(\V{m}\_j | \hat{\V{m}}^{(i)}\_{j,t-1}, \Sigma\_{j,t-1}^{(i)}) } p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t) $</span>
    * 意味　
        * 左側の期待値で表された分布: ランドマークの位置の不確かさを加味したセンサ値のばらつき
        * 右側の分布: 状態遷移

---

### 移動に使う分布の線形化

<span style="font-size:70%">ランドマークの添字は省略</span>

* <span style="font-size:90%">$\eta [\\![ p( \V{z}\_{t} | \V{x}\_t, \V{m}) \mathcal{N}(\V{m} | \hat{\V{m}}^{(i)}\_{t-1}, \Sigma\_{t-1}^{(i)}) ]\\!]\_{\V{m}} p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t) $</span><br />を$\V{x}_t$のガウス分布として近似する
* 手順
    1. 積分の中を$\V{z}\_{t}$の分布$\mathcal{N}(\V{\mu}\_{\V{z}\_t}, Q\_{\V{z\_t}})$に近似<br />
（積分内を$\V{z}\_{t}$と$\V{m}$の分布に分けて$\V{z}\_{t}$の分布を積分外に）
        * $Q\_{\V{z}\_t} = H\_{\V{m}} \Sigma\_{t-1}^{(i)} H\_{\V{m}}^\top + Q\_{\hat{\V{z}}\_t}$
        * $\V{\mu}\_{\V{z}\_t} = \hat{\V{z}}\_t + H\_{\V{x}\_t} (\V{x}\_t - \hat{\V{x}}\_t )$
    2. 1でできた式$\eta \mathcal{N}(\V{z}\_t |\V{\mu}\_{\V{z}\_t}, Q\_{\V{z}\_t}) p(\V{x}\_t | \V{x}^{(i)}\_{t-1}, \V{u}\_t)$について、$\V{x}\_t$のガウス分布$\mathcal{N}(\V{\mu}\_t, \Sigma_t)$に近似
        * 結果は次ページ

---

### 得られる手続き

* $\V{x}_t^{(i)} \sim \mathcal{N}(\V{\mu}_t, \Sigma_t)$
    * ここで
        * $K = R\_t H\_{\V{x}\_t}^\top (Q\_{\V{z}\_t} + H\_{\V{x}\_t} R\_tH\_{\V{x}\_t}^\top)^{-1}$ 
        * $\V{\mu}_t = K(\V{z}_t - \hat{\V{z}}_t ) + \hat{\V{x}}_t$
        * $\Sigma_t = (I - KH_{\V{x}_t}) R_t$
            * 複数のセンサ値が得られたときはこの計算を繰り返す
    * 解釈
        * $\V{x}_t^{(i)}$がドローされる分布の中心は、状態遷移の密度分布の中心の姿勢を、ランドマークの推定位置（分布の中心）から予想されるセンサ値と実際のセンサ値の差に$K$をかけただけ修正した姿勢
        * $\V{x}_t^{(i)}$がドローされる分布の共分散行列は、移動による誤差の共分散行列$R_t$から、センサ値とランドマークの位置の分布から得られる情報だけ<span style="color:red">小さく</span>したもの

センサ値で修正された狭い分布内に$\V{x}_t^{(i)}$が生成

---

## 8.6.2 重みの計算

* 問題
    * ドローされた$\V{x}\_t^{(i)}$にはセンサ値$\V{z}\_{j,t}$の情報が反映されている
$\Longrightarrow$FastSLAM 1.0と同じ重みの計算では二重評価に<br />　
* どうするか
    * 遷移前の姿勢$\V{x}_{t-1}^{(i)}$の尤度を重みにかける
        * 姿勢$\V{x}\_{t-1}^{(i)}$をセンサ値$\V{z}\_{j,t}$で評価
        * 評価が終わってから、$\V{z}\_{j,t}$にもとづいてパーティクルを移動<br />　
* 式: $w\_t^{(i)} = \eta L( \V{x}\_{t-1}^{(i)} | \V{z}\_{j,t},\hat{\textbf{m}}\_{t-1}^{(i)},  \V{u}\_t) w\_{t-1}^{(i)}$

---

### 尤度の計算

* <span style="font-size:90%">$L( \V{x}\_{t-1}^{(i)} | \V{z}\_{j,t},\hat{\textbf{m}}\_{t-1}^{(i)},  \V{u}\_t)$<br />
$\propto p(\V{z}\_t | \V{x}\_{t-1}^{(i)}, \hat{\textbf{m}}\_{t-1}^{(i)}, \V{u}\_t)$<br />
$= [\\![ p(\V{z}\_t, \V{x}\_t | \V{x}\_{t-1}^{(i)}, \hat{\textbf{m}}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$<br />
$= [\\![ p(\V{z}\_t | \V{x}\_t, \V{x}\_{t-1}^{(i)}, \hat{\textbf{m}}\_{t-1}^{(i)}, \V{u}\_t) p(\V{x}\_t | \V{x}\_{t-1}^{(i)}, \hat{\textbf{m}}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$<br />
$= [\\![ p(\V{z}\_t | \V{x}\_t, \hat{\textbf{m}}\_{t-1}^{(i)}) p(\V{x}\_t | \V{x}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$<br />
$= [\\![ p(\V{z}\_t | \V{x}\_t, \hat{\V{m}}\_{t-1}^{(i)}, \Sigma\_{t-1}^{(i)}) p(\V{x}\_t | \V{x}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$<br />
$= [\\![  [\\![ p(\V{z}\_t | \V{x}\_t, \V{m}) \mathcal{N}(\V{m} | \hat{\V{m}}\_{t-1}^{(i)}, \Sigma\_{t-1}^{(i)}) ]\\!]\_{\V{m}} p(\V{x}\_t | \V{x}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$</span>
    * パーティクルの移動に使った分布を$\V{x}\_t$で積分したもの
    * パーティクルの移動に使った分布に対して得られたセンサ値$\V{z}_t$がどれだけ妥当なのかという値に

---

### 重みの計算結果

* スライド5の手順1を行う
    * $L( \V{x}\_{t-1}^{(i)} | \V{z}\_{j,t},\hat{\textbf{m}}\_{t-1}^{(i)},  \V{u}\_t)$<br />
$\propto  [\\![ \mathcal{N}(\V{z}\_t | \V{\mu}\_{\V{z}\_t}, Q\_{\V{z}\_t}) p(\V{x}\_t | \V{x}\_{t-1}^{(i)}, \V{u}\_t) ]\\!]\_{\V{x}\_t}$
        * $Q\_{\V{z}\_t} = H\_{\V{m}} \Sigma\_{t-1}^{(i)} H\_{\V{m}}^\top + Q\_{\hat{\V{z}}\_t}$
        * $\V{\mu}\_{\V{z}\_t} = \hat{\V{z}}\_t - H\_{\V{m}}\hat{\V{m}}^{(i)}\_{t-1} + H\_{\V{x}\_t} (\V{x}\_t - \hat{\V{x}}\_t ) + H\_{\V{m}}\hat{\V{m}}\_{t-1}^{(i)}$<br />　
* $\V{z}_t$と$\V{x}_t$の分布に分離して$\V{z}_t$を積分から出す
    * 結果: $w\_t^{(i)} = \mathcal{N}(\V{z}\_t | \hat{\V{z}}\_t, H\_{\V{x}\_t} R\_t H\_{\V{x}\_t}^\top + Q\_{\V{z}\_t})w\_{t-1}^{(i)}$
    * 解釈
        * センサ値$\V{z}_t$の評価は、状態遷移後の姿勢の分布の中心とランドマークの推定位置の中心から求められるセンサ値$\hat{\V{z}}_t$の値と一致するときに最大に
        * そのときの共分散行列は、移動後の姿勢の不確かさのものと、ランドマークの位置を加味したセンサ値の不確かさのものの和になる


---

## 8.6.3 <span style="text-transform:none">FastSLAM 2.0</span>の実装

* パーティクルの移動にセンサ値を使わないといけないのでFastSLAM 1.0から修正が必要となる
* 左: FastSLAM 1.0、右: FastSLAM 2.0
    * 一見違いはない（近似方法が少し違うだけで元の式は同じ）

<img src="../figs/fastslam_1.0.gif" />
<img src="../figs/fastslam_2.0.gif" />

---

### 30秒後のパーティクル分布の比較

* 上: FastSLAM 1.0、下: FastSLAM 2.0
    * 2.0の方が消えるパーティクルの数が少ない<br />$\Longrightarrow$2.0の方がサンプリングバイアスが小さくなる

<br />
<img width="25%" src="../figs/fastslam1_trial1.png" />
<img width="25%" src="../figs/fastslam1_trial2.png" />
<img width="25%" src="../figs/fastslam1_trial3.png" />
<div style="margin:-35pt">&nbsp;</div>
<img width="25%" src="../figs/fastslam2_trial1.png" />
<img width="25%" src="../figs/fastslam2_trial2.png" />
<img width="25%" src="../figs/fastslam2_trial3.png" />

---

## 8.7 まとめ

* FastSLAM 1.0/2.0を実装
    * リアルタイムに動作する<span style="color:red">逐次SLAM</span>アルゴリズム
        * Rao-Blackwellizationにより大きな次元の確率分布が推定可能に
    * MCLから派生させて実装
        * 推定対象が「姿勢」から「軌跡+地図」に<br />　
* 現在はLiDARと共によく用いられる
    * ROSのgmapping（FastSLAM 2.0）
    * 点ランドマークよりも尤度の計算や地図の作成方法が複雑に
        * キーワード: 尤度場、占有格子地図

