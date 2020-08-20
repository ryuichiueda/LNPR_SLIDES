$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 12. 部分観測マルコフ決定過程<br />（後半）

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## 12.4 AMDP

* 能動的に観測を考慮した行動を生成したい
    * 例
        * ランドマークが観測できるルートをとる
        * 10歩先の信念分布の広がりにあわせて障害物から距離をとる
    * Q-MDPでは無理<br />　
* <span style="color:red">信念状態を状態とみなして価値反復</span>
    * 状態空間を信念分布を表すパラメータで拡張
        * augmented MDP、AMDP
    * 信念分布に価値がつく$\Longrightarrow$状態が確かなほど価値が高く
    * 信念分布が遷移して不確かになっていくことを予測して行動可能

---

### 12.4.1 信念状態空間の離散化

* 状態空間を$XY\theta$空間から$XY\theta\sigma$空間に拡張

