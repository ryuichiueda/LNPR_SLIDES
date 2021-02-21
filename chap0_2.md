$\newcommand{\V}[1]{\boldsymbol{#1}}$

# 読むのための数学2

千葉工業大学 上田 隆一

<p style="font-size:50%">
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a>
</p>

---

## はじめに

* この動画
    * 詳解確率ロボティクスやその他工学書の数式を読み飛ばす学生が多いんじゃないか疑惑<br />　
* なぜか
    * 数式が出てくる本の読み方を教わっていない
    * 高校までの数学が「問題を解く」ことに偏っている
        * 公式 + テクニックもいいけど，それは読解能力にはつながらない
    * 半年単位の講義で急かされて勉強するのでじっくり理解するという習慣がつきにくい


$\Rightarrow$「読む」に焦点を当てて数式を解説

---

## 今回の内容: 関数（写像）

* $y = f(x)$のアレ
* 前回の集合を使って考える
* （関数 $\fallingdotseq$ 写像として話をします）

---

## じゃんけん

* じゃんけんの手の集合を考える
  * $A$ = {グー，チョキ，パー}
    * この表記がわからない人は前回の動画に強制送還<br />　
* 各手に対して勝つ手をどう表現するか？
  * これでよい？（面倒！）
    * グー$\rightarrow$パー
    * チョキ$\rightarrow$グー
    * パー$\rightarrow$チョキ

「勝つ手」に相当する記号がほしい

---

## 「勝つ手」の記号と利用

* 記号$f$を定義する
  * $f$のルール: 
    * 「$f$(グー)」 と書くと、それは「パー」のこと
    * 「$f$(チョキ)」 と書くと、それは「グー」のこと
    * 「$f$(パー)」 と書くと、それは「チョキ」のこと<br />　
  * ある人の手を$a \in A$とおくと、その手に勝つ手は$f(a)$
    * $a$が不定でも、その手に勝つ手を表現可能<br />　

---


## $f$(なんとか)

* なんとかは「じゃんけんの手」のどれか
  * なんとか$\in A$
    * 「なんとか」をいつまでも「なんとか」と書くのは面倒<br />　
* 「なんとか」を$a$とおくと、$f$の性質を記号で説明可能
  * $f(a) \quad (\forall a \in A)$
    * $f(a)$は任意のじゃんけんの手$a$に対して存在
    * 工学的: $f$への入力は任意のじゃんけんの手
    * 情報的: $f$の引数は任意のじゃんけんの手
  * $f(a) \in A$
    * $f(a)$もじゃんけんの手のひとつ

---

## $f$(なんとか)

* <span style="color:red">$f: A \rightarrow A$</span>

