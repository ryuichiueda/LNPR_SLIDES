$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 9. グラフ表現によるSLAM（前半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### グラフベースSLAM

* グラフ<span style="font-size:50%">（グラフ理論のもの）</span>を用いたSLAM
    * ノード（頂点）: ロボットの姿勢やランドマークの位置 
    * エッジ（辺）: ノードの相対位置情報
        * デッドレコニング、センサ値<br />
<img width="30%" src="./figs/graph.jpg" />
* グラフに「歪み」
    * エッジの相対位置情報が互いに矛盾
    * 歪みを最小化するようにノードを移動$\Longrightarrow$最尤な地図と軌跡

---

## 9.1 問題の定式化

* 8章のSLAMの式（少し改変）からスタート
    * $p(\V{x}\_{1:T}, \textbf{m} | \V{x}\_0, \V{u}\_{1:T}, \textbf{z}\_{0:T})$
        * $T$: ロボットが移動、観測を終了する時刻
        * $\textbf{z}_0$の存在を仮定（あとで不要に）<br />　
* FastSLAM同様、軌跡の分布と地図の分布に分離
    * $p(\boldsymbol{x}\_{1:T}, \textbf{m} | \boldsymbol{x}\_0, \boldsymbol{u}\_{1:T}, \textbf{z}\_{0:T}) = p(\boldsymbol{x}\_{1:T} | \boldsymbol{x}\_0, \boldsymbol{u}\_{1:T}, \textbf{z}\_{0:T}) p(\textbf{m} | \boldsymbol{x}\_{0:T}, \textbf{z}\_{0:T}) $<br />　
* FastSLAMと異なり、次の手順を踏む
    * $\boldsymbol{x}\_{1:T}^\* = \text{argmax}\_{\boldsymbol{x}\_{1:T}} p(\boldsymbol{x}\_{1:T} | \boldsymbol{x}\_0, \boldsymbol{u}\_{1:T}, \textbf{z}\_{0:T})$で軌跡を算出
    * $\textbf{m}^\* = \text{argmax}\_{\textbf{m}} p(\textbf{m} | \V{x}\_0, \boldsymbol{x}\_{1:T}^*, \textbf{z}\_{0:T})$で地図を算出

推定というより最適化

---

## 9.1.1 軌跡の算出問題

* 解く問題
    * $\boldsymbol{x}\_{0:T}^\* = \text{argmax}\_{\boldsymbol{x}\_{0:T}} p(\boldsymbol{x}\_{0:T} | \hat{\boldsymbol{x}}\_0, \boldsymbol{u}\_{1:T}, \textbf{z}\_{0:T})$
        * 前ページと違って$\V{x}_0$を変数に
        * 条件にあった$\V{x}_0$は$\hat{\V{x}}_0$に
            * 下記のように初期値を条件とする
    * <span style="color:red">初期値$\hat{\boldsymbol{x}}\_{0:T}$を決めて$\boldsymbol{x}\_{0:T}^\*$まで探索</span><br />　
* 初期値$\hat{\boldsymbol{x}}\_{0:T}$の決め方
    * 雑音を考慮せず状態方程式で$\hat{\boldsymbol{x}}\_{0:T}$を決める<br />　
* グラフ作成
    * 各初期値をノード、時刻が前後するノードを接続してエッジに

---

### ポーズ調整

* 軌跡を求めるアルゴリズム
    * グラフベースSLAMの手法で、本書で扱う
    * ランドマークの位置はノードにせず姿勢だけをノードに

