## 2. 確率統計の基礎

千葉工業大学 上田 隆一
2019年4月24日

<br />

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

### 実験

* ロボットを決めた距離だけ壁から離して置く
* 3秒ごとにセンサの値を記録（2日間）
    * 光センサとLiDARの1本のレーザー

<img width="30%" src="../figs/sensor_experiment.jpg" />

---

### 得られたデータ

* [200mm](https://raw.githubusercontent.com/ryuichiueda/LNPR_BOOK_CODES/master/section_sensor/sensor_data_200.txt)
    * $1: 日付
    * $2: 時分秒
    * $3: 光センサの値
    * $4: LiDAR（の1本のレーザの）値
* LiDARの値の特性を調査してみましょう
    * こんなにたくさんデータあるけどどうしよう？
        * 統計学
    * 以後、LiDARから得た値を「センサ値」と呼称

---

### 度数分布・ヒストグラム

* 度数分布
    * センサ値の範囲をいくつかの区間に分ける
    * 各区間に属するセンサ値の数を集計
* ヒストグラム
    * 度数分布をグラフにしたもの
    * 下図: 区間の幅1で描いたヒストグラム

<img width="50%" src="../figs/sensor_200_histgram.png" />

---
