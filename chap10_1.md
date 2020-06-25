$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 10. マルコフ決定過程と動的計画法

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### ロボットの行動決定

* 移動ロボットの場合、経路計画問題として扱われることが多い
    * ある地点からある地点までの移動を最短経路で<br />　
* もっと知的にしたい
    * 例
        * 危険のある近道と危険でない回り道を柔軟に選択
        * 目的地に行くことを諦める

<span style="color:red">マルコフ決定過程</span>と<span style="color:red">動的計画法</span>の枠組みで考える

---

## 10.1 マルコフ決定過程

* やること
    * ロボットが行動決定するという現象や問題を定式化


---

## 10.1.1 状態遷移と観測

* ロボットの行動
    * 状態遷移モデル: $\V{x}\_t \\sim p(\V{x} | \V{x}\_{t-1}, a\_t)$<br />$(t=1,2,\\dots,T)$
        * $T$: タスクが終わる時間（不定）
        * $\V{x}_T$: 終端状態
        * $a_t \in \mathcal{A} = \\{a_0, a_1, \dots, a_{M-1}\\}$: 行動
            * これまでのようにベクトルでもよいし、「走る」、「投げる」などの複合的な動作でもよい。なんなら「食う」、「寝る」、「遊ぶ」などでもよい
            * 添字が時刻だったり行動の種類のIDだったりするので注意<br />　
* ロボットの観測
    * 常に真の状態$\V{x}_t^*$が既知とする
    * 未知の場合は12章で扱う


---

### マルコフ性の仮定

* 状態遷移が遷移前の状態よりも前の状態に左右されない場合を考える
    * $\V{x}\_t \\sim p(\V{x} | \V{x}\_{t-1}, a\_t)$の形を見るとそうなっている
    * $\V{x}\_{t-2}$以前の状態は何の影響も与えない<br />　
* そうでない場合はどうする？
    * マルコフ性を満たすように状態の定義をしなおす
    * 例: $\V{x}\_{t-2}$まで散々動いてモータが熱々のロボットと$\V{x}\_{t-1}$からスタートするロボットでは動きが違う<br />
    $\Longrightarrow$モータの温度を$\V{x}$の変数として扱う



---

## 10.1.2 評価関数

* ロボットの行動を評価する
    * これまでは自己位置推定やSLAMがうまくできるかどうかが評価だったが、タスクをうまく遂行できるかどうかが評価に<br />　
* 評価関数: $J(\boldsymbol{x}\_{0:T}, a\_{1:T}) \in \Re$
    *  評価は時間、消費エネルギー、危険性など多岐にわたるはずであるが、スカラ1個で評価することとする

