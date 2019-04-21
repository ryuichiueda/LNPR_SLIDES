## 1. 確率ロボティクスとは

千葉工業大学 上田 隆一

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 世の中の「分からない」

* 世の中は分からないことだらけ
    * 壁の向こうは見えない
    * 未来のことは分からない
    * 難しい話をいきなり聞いても分からない

<br />
<img width="20%" src="../figs/kabedon_old.png" />
<span style="font-size:50%">壁を叩いていいものか？</span>

---

### 世の中の難しさ

* 分からないことだらけでも何かの決定・実行が必要
    * 時限爆弾を止めるためにどっちの線を切る？
* 分からないと行動できないわけでもない
    * プログラムをいじってたらいつの間にか動いた
    * 真っ暗な建物に閉じ込められたが何とか脱出した
    * 経営よく分からないけどいつの間にか社長に

<p style="color:red">このようなことを扱うにはどうすればよいか？</p>

---

### 確率: 分からないを扱う道具

* 工学における古典的な確率の利用法
    * 「ロボットの位置は$x = 100 \pm 10$[mm]」
        * 位置の不確かさを誤差の範囲という形式で表現
        * 誤差の範囲は正規分布の標準偏差であることが多い

---

### もっと積極的に確率を使う

* 例えばこのような表現
    * ロボットが2階にいる確率は80%。他の階にいる確率は20%
    * ロボットの位置は3階のどこか
        * 3階のどこかは分からない（一様分布での表現になる）

---

### どのように役立つか

* 分からないなら調べようという動機
* 分からないまま行動
    * 何回にいても1階に行くには階段を探せばよい
* 分からないということを把握<span style="color:red">（メタ認知）</span>
    * 実世界では過信はろくなことにならない

---

### 確率ロボティクス

* 今のような話をロボティクスに持ち込んだもの 
* 扱われる問題
    * 自己位置推定
    * 地図生成・SLAM
    * 行動決定（ナビゲーションや探査）

