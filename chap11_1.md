$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 11. 強化学習<br />（前半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 強化学習

* MDPにおいて以下の情報だけで方策を得る
    *  行動と行動の集合: $a \in \mathcal{A}$
    *  現在時刻までのエピソード: $\\{\V{x}_0, a_1, \V{x}_1, r_1, a_2, \V{x}_2, r_2, \dots, a_t, \V{x}_t, r_t \\}$
    *  終端状態に着いたこと, およびそのときに得られる価値$v_\text{f}$
    *  評価: $J(\V{x}\_{0:T}, a\_{1:T}) = \sum\_{t=1}^T r\_t + v\_\text{f}$

エージェントを動かして状態遷移や報酬の統計をとらないと解けない$\Longrightarrow$<span style="color:red">強化学習</span>の問題
