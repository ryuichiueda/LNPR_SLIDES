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
  * 定義: $\circ\circ\circ\dots\circ$のことを$\times\times$と表すことにすること
    * じゃんけんの各手に対して勝つ手を決めたルールを$f$と表す
  * $f$のルール: 
    * 「$f$(グー)」 と書くと、それは「パー」のこと
    * 「$f$(チョキ)」 と書くと、それは「グー」のこと
    * 「$f$(パー)」 と書くと、それは「チョキ」のこと<br />　
  * ある人の手を$a \in A$とおくと、その手に勝つ手は$f(a)$
    * $a$が不定でも、その手に勝つ手を表現可能

---


## $f$とは何か。どう読むか。

* 前のスライドのとおり「ルール（規則）」を一文字で置き換えたもの
  * これを<span style="color:red">関数</span>と呼ぶ
    * 工学的には、何かを入力すると何かを出力するもの
    * プログラミングでは、なにかを引数にとって、何かを返すもの<br />　
* 1文字で表す = 余計なことを忘れる
    * 「$a$がじゃんけんの手で$f(a)$がそれに勝つじゃんけんの手」
      * 具体的に$a$がなにで$f(a)$がなんなのかを考えていると読めなくなる

---

## 忘れてはいけないこと

* 関数で大事なのは<span style="color:red">入出力がなにか</span>
  * 「$a$がじゃんけんの手で$f(a)$がそれに勝つじゃんけんの手」
    * $f$がじゃんけんの手に対してじゃんけんの手を返すという前提知識があるから読める<br />　
* 入出力の表記
  * <span style="color:red">$f: A \rightarrow A \quad$（$A: $じゃんけんの手の集合）</span>
    * 「$f$は$A$の要素を$A$の要素に変換する関数（写す写像）」
    * 重要なので$f$の定義の際に書いてある
      * $f(a)$って何だっけ、となったときに、ここに戻る

---

## 例1: $y = 2x$

* 話し言葉では関数だけど、これは等式
  * 関数が明記されていない
  * この等式の背景にある関数は？
    * $x$が入力で$y$が出力<br />　
* ちゃんとした関数の表記
  * <span style="color:red">$f: \mathbb{R} \to \mathbb{R}$<br />
$\quad\ \ x \mapsto 2x$</span>
    * $\mathbb{R}$: 実数の集合（必ずしも実数である必要はなく複素数でもよい）
  * ポイント
    * $x \in \mathbb{R}$
    * $y = 2x$は$y = f(x)$と書ける（$f$という名前がつく）
  * <span style="color:red">$f(x) = 2x \ (\forall x \in \mathbb{R})$</span>と書いてもよい
    * ただし、あくまで関数は$f$で、$f(x)$は値

---

## 例2: $f(x,y) = 2x + 3y$

* どの集合からどの集合への関数？
  * $x \in \mathbb{R}, y \in \mathbb{R}$としましょう<br />　
* こたえ
  * $f: \mathbb{R}\times \mathbb{R} \to \mathbb{R}$
    * $\mathbb{R}$から実数をふたつ選んで入力すると実数を出力<br />　
* 補足（プログラミングでの表現）
  * C/C++だと「double f(double x, double y)」
  * Haskellだと「f :: Double -> Double -> Double」

---

## まとめ

* 関数
    * 工学的には「何かを入力したら、何かを出力するもの」<br />　
* 表記をちゃんと読めるようにしましょう
    * 表記の例
        * $f: \mathbb{R} \times \mathbb{R} \to \mathbb{R}$<br />$\quad\quad\ \ x,y \mapsto 2x+3y$
        * 大事なことが全部含まれる
            * なにを入力対象とするのか（プログラミングでは入力の型）
            * なにを出力するのか（プログラミングでは返す値の型）
            * どんなふうに変換するのか（プログラミングでは実装）

