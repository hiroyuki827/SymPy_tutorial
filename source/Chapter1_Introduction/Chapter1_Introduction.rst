** なぜ\ ``Sympy``\ か? **
===========================
-  SymPyは完全にフリーソフトウェア.
   MapleやMathematicaのようにお金をかけなくて良い.

-  Pythonをベースにしているから. 完全にPython上で実行される.
   PythonのSyntaxも同様に使える.

-  Sage等もPythnoを使っているが、ダウンロード容量が大変多い.
   それに対してSymPyは非常に軽量.
   Python以外に依存するソフトウェアがない.

-  ライブラリとして使える.
   Pythonでimportするだけでいろいろな機能が利用可能になる.

Chapter 1 Introduction
======================

本章では、SymPyではどういうことができるのかを見ていく。詳しい内容は次章以降で見ていくので、ここではざっと読み通す程度で良い。

**用語について**:
チュートリアル原文では、SymPyで扱う変数を\ *SymPyシンボル*\ と呼んでいます。Pythonではシンボルに対応する概念がないのですが、チュートリアルに従っています。\ *SymPyオブジェクト*\ と呼んだときは、\ *SymPy*\ で定義されている関数・メソッドや、\ *SymPy*\ シンボル等を含めて総称として呼ぶことにします。

1.1 シンボリックな計算とは何か?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pythonの標準ライブラリ\ ``math``\ モジュールでは

.. code:: ipython3

    import math

.. code:: ipython3

    math.sqrt(9)




.. parsed-literal::

    3.0



という計算ができる。では8はどうか？この場合は

.. code:: ipython3

    math.sqrt(8)




.. parsed-literal::

    2.8284271247461903



となり、近似値を与える。 ただよく知っているように\

.. math:: \sqrt{8}=2\sqrt{2}

\ である。この結果は上の計算からは導けない。つまり、より式を簡単にして、見慣れた形にしてから近似値を出したい。->
シンボリックな計算の必要性. ``Sympy``\ ならば

.. code:: ipython3

    import sympy
    sympy.sqrt(3)




.. parsed-literal::

    sqrt(3)



と出してくれる。\ **近似値ではなく、数式として簡単なものがほしいとき**\ に、\ ``Sympy``\ が使える。

1.2 より面白い例
~~~~~~~~~~~~~~~~

``Sympy``\ は方程式も扱える:

.. code:: ipython3

    from sympy import symbols #一変数に対してもsymbolsが使える.

.. code:: ipython3

    x, y = symbols('x, y') #変数として扱いたいものにsymbols()関数を使う.

.. code:: ipython3

    expr = x + 2*y

.. code:: ipython3

    expr




.. parsed-literal::

    x + 2*y



``x``,
``y``\ がPythonオブジェクトならば、これらに値が格納されていないと意味をなさない。(次章から詳しく見ていくように、エラーを出す。)
一方で\ ``Sympy``\ シンボルとして(\ ``symbols``\ を用いて)宣言すれば、それらは変数の格納有無にかかわらず、数学で私達が扱っているように、変数そのものとして扱うことができるようになる。

*注意*
上で扱った変数に対して、以下のように\ ``Python``\ オブジェクトの代入操作を行うと、

.. code:: ipython3

    x = 4
    y = 2

.. code:: ipython3

    expr




.. parsed-literal::

    x + 2*y



となって、\ ``expr``\ に代入されている\ ``Sympy``\ オブジェクトには何ら影響を与えない。(変数への代入は、\ ``subs()``\ を用いる。(後述)
)

--> この変数\ ``expr``\ を使えば、変数\ ``x``, ``y``\ を用いて、

.. code:: ipython3

    expr + 1




.. parsed-literal::

    x + 2*y + 1



.. code:: ipython3

    expr - x




.. parsed-literal::

    x + 2*y - 4



といった計算もできる。ところで、文字同士の計算にもかかわらず、\ ``x + 2*y - x``\ といった結果は出なかった。
**変数同士の演算も可能**

予想されるように、\ ``expr``\ に\ ``x``\ をかけてみると、

.. code:: ipython3

    x*expr




.. parsed-literal::

    4*x + 8*y



を得る。

展開した式がほしいときは、\ ``expand``\ 関数を用いて

.. code:: ipython3

    from sympy import expand

.. code:: ipython3

    expr_expand = expand(x*expr)

.. code:: ipython3

    expr_expand




.. parsed-literal::

    4*x + 8*y



とすればよい。

逆に因数をまとめて整理したいときは、\ ``factor``\ 関数を用いて

.. code:: ipython3

    from sympy import factor

.. code:: ipython3

    factor(expr_expand)




.. parsed-literal::

    4*(x + 2*y)



とすればよい。このように自由に表式を行き来できる。

1.4 強力なシンボリック計算
~~~~~~~~~~~~~~~~~~~~~~~~~~

Sympyでは\ **行列計算、極限、微分、積分、方程式**\ を解く、その他もろもろが可能.
LaTeXでグラフや式の出力も可能。幅広い数学の計算ができる！

.. code:: ipython3

    from sympy import * #すべての関数をimport

.. code:: ipython3

    x, t, z, nu = symbols('x, t, z, nu')

.. code:: ipython3

    init_printing(use_unicode=True) #unicode文字を出力するため.

☆関数\

.. math:: \sin(x)e^{2}

\ の微分

.. code:: ipython3

    diff( sin(x)*exp(x), x)




.. math::

    e^{x} \sin{\left (x \right )} + e^{x} \cos{\left (x \right )}



☆不定積分:

.. math:: \int \, (e^{x}\sin(x) + e^{x}\cos(x))\, dx

.. code:: ipython3

    integrate(exp(x)*sin(x) + exp(x)*cos(x), x)




.. math::

    e^{x} \sin{\left (x \right )}



☆定積分:\

.. math:: \int_{-\infty}^{\infty}\sin(x^{2})dx

.. code:: ipython3

    integrate(sin(x**2), (x, -oo, oo)) #無限大はo(オー)２つ.




.. math::

    \frac{\sqrt{2} \sqrt{\pi}}{2}



☆極限:

.. math:: \lim_{x\rightarrow 0}\frac{\sin(x)}{x}

.. code:: ipython3

    limit(sin(x)/x, x, 0)




.. math::

    1



☆方程式:\

.. math:: x^2-2=0

\ を解く

.. code:: ipython3

    solve(x**2 - 2, x)




.. math::

    \left [ - \sqrt{2}, \quad \sqrt{2}\right ]



☆微分方程式:\

.. math:: \frac{d^2y}{dt^2}-y=e^{t}

\ をyについて解く.

.. code:: ipython3

    y = Function('y')

.. code:: ipython3

    dsolve(Eq(y(t).diff(t,t) - y(t), exp(t)), y(t))




.. math::

    y{\left (t \right )} = C_{2} e^{- t} + \left(C_{1} + \frac{t}{2}\right) e^{t}



☆行列:\

.. math::

   \left[
       \begin{array}{cc}
       1 & 2 \\
       2 & 2 \\
       \end{array}\right]


\ の固有値を見つける.

.. code:: ipython3

    Matrix([[1, 2],
            [2, 2]]).eigenvals() #行列はこう書いたほうがわかりやすい。




.. math::

    \left \{ \frac{3}{2} + \frac{\sqrt{17}}{2} : 1, \quad - \frac{\sqrt{17}}{2} + \frac{3}{2} : 1\right \}



☆ベッセル関数\

.. math:: J_{\nu}(z)

\ を球ベッセル関数\

.. math:: j_{\nu}(z)

\ を用いて書く.

.. code:: ipython3

    besselj(nu, z).rewrite(jn)




.. math::

    \frac{\sqrt{2} \sqrt{z}}{\sqrt{\pi}} j_{\nu - \frac{1}{2}}\left(z\right)



☆式:

.. math:: \int_{0}^{\pi}\cos^2(x)dx

\ を\

.. math:: \LaTeX

\ で出力.

.. code:: ipython3

    latex(Integral(cos(x)**2, (x, 0, pi)))




.. parsed-literal::

    '\\int_{0}^{\\pi} \\cos^{2}{\\left (x \\right )}\\, dx'
