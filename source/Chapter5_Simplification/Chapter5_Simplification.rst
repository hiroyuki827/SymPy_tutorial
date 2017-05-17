
Chapter 5 Simplifications
=========================

.. code:: ipython3

    from sympy import *
    x, y, z = symbols('x, y, z')
    init_printing(use_unicode=True)

5.1 単純化
----------

``sympy``\ のどんな数式も\ ``simplify()``\ で簡単な形にできる！:

.. code:: ipython3

    simplify(sin(x)**2 + cos(x)**2)




.. math::

    1



.. code:: ipython3

    simplify((x**3 + x**2 - x - 1) / (x**2 + 2*x + 1))




.. math::

    x - 1



.. code:: ipython3

    simplify(gamma(x) / gamma(x-2)) #ガンマ関数(特殊関数)




.. math::

    \left(x - 2\right) \left(x - 1\right)



注意点:その1
^^^^^^^^^^^^

.. code:: ipython3

    simplify(x**2 + 2*x + 1)




.. math::

    x^{2} + 2 x + 1



---> **因数分解できない!!!** 因数分解は\ ``factor()``\ 関数を使う:

.. code:: ipython3

    factor(x**2 + 2*x + 1)




.. math::

    \left(x + 1\right)^{2}



注意点:その2
^^^^^^^^^^^^

``simplify()``\ は遅い！

解決策
^^^^^^

-  ``simplify()``\ は「ある程度」簡単な形にまでしか変形できないので、確実に式を簡単にしたいなら、その用途に応じた適切な関数を使うべき!

-  インタラクティブシェルで\ ``simplify``\ の挙動を見てから\ **個別の関数**\ (以下)
   を使って簡単にしよう.

5.2 多項式 / 有理式
-------------------

5.2.1 ``expand``\ 関数
~~~~~~~~~~~~~~~~~~~~~~

多項式を展開し、必要ならば項をキャンセルする.

.. code:: ipython3

    expand((x + 1)**2)




.. math::

    x^{2} + 2 x + 1



.. code:: ipython3

    expand((x + 2)*(x - 3))




.. math::

    x^{2} - x - 6



「式を展開する」ことで「式が簡単になる」ことがある。

.. code:: ipython3

    expand((x + 1)*(x - 2) - (x - 1)*x) #式がキャンセルし合う




.. math::

    -2



5.2.2 ``factor``\ 関数
~~~~~~~~~~~~~~~~~~~~~~

数式を可能な限り因数分解する

.. code:: ipython3

    factor(x**3 - x**2 + x - 1)




.. math::

    \left(x - 1\right) \left(x^{2} + 1\right)



.. code:: ipython3

    factor(x**2*z + 4*x*y*z + 4*y**2*z)




.. math::

    z \left(x + 2 y\right)^{2}



.. code:: ipython3

    factor_list(x**2*z + 4*x*y*z + 4*y**2*z) #(変数or定数, べき)




.. math::

    \left ( 1, \quad \left [ \left ( z, \quad 1\right ), \quad \left ( x + 2 y, \quad 2\right )\right ]\right )



三角関数程度の式なら、関数\ ``factor``, ``expand``\ で対応可能
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    expand((cos(x) + sin(x))**2)




.. math::

    \sin^{2}{\left (x \right )} + 2 \sin{\left (x \right )} \cos{\left (x \right )} + \cos^{2}{\left (x \right )}



.. code:: ipython3

    factor(cos(x)**2 + 2*cos(x)*sin(x) + sin(x)**2)




.. math::

    \left(\sin{\left (x \right )} + \cos{\left (x \right )}\right)^{2}



5.2.3 ``collect``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~

特定の変数でまとめたり、特定次の係数を取り出す.

.. code:: ipython3

    expr = x*y + x -3 + 2*x**2 - z*x**2 + x**3

.. code:: ipython3

    expr




.. math::

    x^{3} - x^{2} z + 2 x^{2} + x y + x - 3



.. code:: ipython3

    collected_expr = collect(expr, x) #xでまとめる.

.. code:: ipython3

    collected_expr




.. math::

    x^{3} + x^{2} \left(- z + 2\right) + x \left(y + 1\right) - 3



さらに以下のようにcoeffメソッドで特定次を取り出せる.

.. code:: ipython3

    collected_expr.coeff(x, 2) #xの2次だけ取り出す.




.. math::

    - z + 2



5.2.4 ``cancel``\ 関数
~~~~~~~~~~~~~~~~~~~~~~

有理式を簡単にする

.. code:: ipython3

    cancel((x**2 + 2*x + 1) / (x**2 + x)) 




.. math::

    \frac{1}{x} \left(x + 1\right)



.. code:: ipython3

    expr = 1/x + (2*x/2 - 2) /(x - 4) 

.. code:: ipython3

    expr




.. math::

    \frac{x - 2}{x - 4} + \frac{1}{x}



.. code:: ipython3

    cancel(expr) #分母を通分する




.. math::

    \frac{x^{2} - x - 4}{x^{2} - 4 x}



.. code:: ipython3

    factor(expr) #factorも同じような操作をする.




.. math::

    \frac{x^{2} - x - 4}{x \left(x - 4\right)}



.. code:: ipython3

    expr = (x*y**2 - 2*x*y*z + x*z**2 + y**2 - 2*y*z + z**2) / (x**2 - 1)

.. code:: ipython3

    expr




.. math::

    \frac{1}{x^{2} - 1} \left(x y^{2} - 2 x y z + x z^{2} + y^{2} - 2 y z + z^{2}\right)



.. code:: ipython3

    cancel(expr)




.. math::

    \frac{1}{x - 1} \left(y^{2} - 2 y z + z^{2}\right)



.. code:: ipython3

    factor(expr) #factorも同じような変形をする.




.. math::

    \frac{\left(y - z\right)^{2}}{x - 1}



**コメント**

式を単にキャンセルさせてシンプルにさせたいときは、\ ``factor()``\ より\ ``cancel()``\ のほうが効率的

5.2.5 ``apart``\ 関数
~~~~~~~~~~~~~~~~~~~~~

有理式(分数)を部分分数分解する

.. code:: ipython3

    x = symbols('x')
    expr = (4*x**3 + 21*x**2 + 10*x + 12) / (x**4 + 5*x**3 + 5*x**2 + 4*x)

.. code:: ipython3

    expr




.. math::

    \frac{4 x^{3} + 21 x^{2} + 10 x + 12}{x^{4} + 5 x^{3} + 5 x^{2} + 4 x}



.. code:: ipython3

    apart(expr)




.. math::

    \frac{2 x - 1}{x^{2} + x + 1} - \frac{1}{x + 4} + \frac{3}{x}



5.3 三角関数
------------

**コメント**: 逆三角関数は頭に"a"を付ける: acos, asin, atan, etc...

.. code:: ipython3

    acos(x)




.. math::

    \operatorname{acos}{\left (x \right )}



.. code:: ipython3

    cos(acos(x))




.. math::

    x



.. code:: ipython3

    asin(1)




.. math::

    \frac{\pi}{2}



5.3.1 ``trigsimp``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~

三角関数の表式を、公式を用いて可能な限りシンプルな形にする.

.. code:: ipython3

    trigsimp(sin(x)**2 + cos(x)**2)




.. math::

    1



.. code:: ipython3

    trigsimp(sin(x)**4 - 2*cos(x)**2*sin(x)**2 + cos(x)**4)




.. math::

    \frac{1}{2} \cos{\left (4 x \right )} + \frac{1}{2}



.. code:: ipython3

    trigsimp(sin(x)*tan(x)/sec(x))




.. math::

    \sin^{2}{\left (x \right )}



.. code:: ipython3

    trigsimp(cosh(x)**2-sinh(x)**2)




.. math::

    1



5.3.2 ``expand_trig``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~

三角関数の式を展開する。
``trigsimp``\ と\ ``expand_trig``\ は完全に逆の操作をする

.. code:: ipython3

    expand_trig(sin(x + y))




.. math::

    \sin{\left (x \right )} \cos{\left (y \right )} + \sin{\left (y \right )} \cos{\left (x \right )}



.. code:: ipython3

    expand_trig(tan(2*x))




.. math::

    \frac{2 \tan{\left (x \right )}}{- \tan^{2}{\left (x \right )} + 1}



5.4 べき乗
----------

.. code:: ipython3

    x, y = symbols('x y', positive=True) #変数が正であると仮定

.. code:: ipython3

    a, b = symbols('a, b', real = True) #変数が実数であると仮定

.. code:: ipython3

    z, t, c = symbols('z t c')

**コメント**: ``sqrt(x)``\ と\ ``x**Rational(1,2)``, ``x**0.5``,
``x**(1/2)``\ は同じ

.. code:: ipython3

    sqrt(x)




.. math::

    \sqrt{x}



.. code:: ipython3

    x**Rational(1,2)




.. math::

    \sqrt{x}



.. code:: ipython3

    x**(0.5)




.. math::

    x^{0.5}



.. code:: ipython3

    x**(1/2)




.. math::

    x^{0.5}



5.4.1 ``powsimp`` 関数
~~~~~~~~~~~~~~~~~~~~~~

冪が変数(\ ``Sympy``\ シンボル)のときに限り、シンプルな形にする

.. code:: ipython3

    powsimp(x**a*x**b) #これ以上簡単にできない.




.. math::

    x^{a + b}



.. code:: ipython3

    powsimp(x**a*y**a)




.. math::

    \left(x y\right)^{a}



変数の仮定にかかわらず実行させたいとき:

.. code:: ipython3

    powsimp(t**c*z**c)




.. math::

    t^{c} z^{c}



を

.. code:: ipython3

    powsimp(t**c*z**c, force=True)




.. math::

    \left(t z\right)^{c}



とする. ``t`` もしくは ``z`` が負になっても強制的にこの変形は行われる.

.. code:: ipython3

    (z*t)**2 #冪が整数、有理数, 2のとき.




.. math::

    t^{2} z^{2}



.. code:: ipython3

    sqrt(x*y) #同じ




.. math::

    \sqrt{x} \sqrt{y}



**注意** このような式に対しては\ ``powsimp``\ は使えない:

.. code:: ipython3

    powsimp(z**2*t**2) #指数が整数




.. math::

    t^{2} z^{2}



.. code:: ipython3

    sqrt(x*y)




.. math::

    \sqrt{x} \sqrt{y}



--->冪が変数のときに``powsimp``\ で簡単にできる.

5.4.2 ``expand_power_expr``\ 関数, ``expand_power_base``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

べき乗を展開する. ``powsimp``\ 関数と逆の操作

.. code:: ipython3

    expand_power_exp(x**(a + b))




.. math::

    x^{a} x^{b}



.. code:: ipython3

    expand_power_base((x*y)**a)




.. math::

    x^{a} y^{a}



**注意**
これも\ ``powsimp()``\ と同様で、変形できないときは元の式を返す:

.. code:: ipython3

    expand_power_base((z*t)**c)




.. math::

    \left(t z\right)^{c}



``t*z``\ が正という条件を\ ``symbols``\ でつけていれば展開できるが、
今回のようにそうと限らないときは展開してくれない. 強制的に行うには

.. code:: ipython3

    expand_power_base((z*t)**c, force=True)




.. math::

    t^{c} z^{c}



とする. また冪が数のときは

.. code:: ipython3

    x**2*x**3




.. math::

    x^{5}



.. code:: ipython3

    expand_power_exp(x**5)




.. math::

    x^{5}



のように変形できない。

5.4.3 ``powdenest``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~~

べき乗のべき乗を展開

.. code:: ipython3

    (x**a)**b #カッコを外して展開




.. math::

    x^{a b}



.. code:: ipython3

    powdenest((x**a)**b)




.. math::

    x^{a b}



.. code:: ipython3

    powdenest((z**a)**b)




.. math::

    \left(z^{a}\right)^{b}



.. code:: ipython3

    powdenest((z**a)**b, force=True)




.. math::

    z^{a b}



5.5 指数関数、対数関数
----------------------

.. code:: ipython3

    ln(x) #ln(x)とlog(x)は同じ.




.. math::

    \log{\left (x \right )}



.. code:: ipython3

    log(x)




.. math::

    \log{\left (x \right )}



.. code:: ipython3

    x, y = symbols('x y', positive=True)

.. code:: ipython3

    n = symbols('n', real=True)

5.5.1 ``expand_log``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~~~

対数関数を展開する

.. code:: ipython3

    expand_log(log(x*y))




.. math::

    \log{\left (x \right )} + \log{\left (y \right )}



.. code:: ipython3

    expand_log(log(x/y))




.. math::

    \log{\left (x \right )} - \log{\left (y \right )}



.. code:: ipython3

    expand_log(log(x**2))




.. math::

    2 \log{\left (x \right )}



.. code:: ipython3

    expand_log(log(x**n))




.. math::

    n \log{\left (x \right )}



.. code:: ipython3

    expand_log(log(z*t))




.. math::

    \log{\left (t z \right )}



**注意**
これまでと同様にして、正でない変数は展開できないので、そのときは\ ``Force=True``\ オプションを付ける。

.. code:: ipython3

    expand_log(log(z**2))




.. math::

    \log{\left (z^{2} \right )}



.. code:: ipython3

    expand_log(log(z**2), force=True)




.. math::

    2 \log{\left (z \right )}



5.5.2 ``logcombine``\ 関数
~~~~~~~~~~~~~~~~~~~~~~~~~~

対数関数をシンプルにする.

.. code:: ipython3

    logcombine(log(x) + log(y)) #対数関数を簡単にする




.. math::

    \log{\left (x y \right )}



.. code:: ipython3

    logcombine(n*log(x))




.. math::

    \log{\left (x^{n} \right )}



.. code:: ipython3

    logcombine(n*log(z))




.. math::

    n \log{\left (z \right )}



.. code:: ipython3

    logcombine(n*log(z), force=True)




.. math::

    \log{\left (z^{n} \right )}



5.6 特殊関数
------------

.. code:: ipython3

    x, y, z = symbols('x y z')

.. code:: ipython3

    k, m, n = symbols('k m n')

5.6.1 階乗
~~~~~~~~~~

.. code:: ipython3

    factorial(n)




.. math::

    n!



.. code:: ipython3

    factorial(10)




.. math::

    3628800



5.6.2 組み合わせ (Combination)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    binomial(n, k) #nCk




.. math::

    {\binom{n}{k}}



.. code:: ipython3

    combsimp(factorial(n) / factorial(n - 3)) #シンプルにする




.. math::

    n \left(n - 2\right) \left(n - 1\right)



.. code:: ipython3

    combsimp(binomial(n + 1, k + 1) / binomial(n, k))




.. math::

    \frac{n + 1}{k + 1}



5.6.3 ガンマ関数
~~~~~~~~~~~~~~~~

.. code:: ipython3

    gamma(z)




.. math::

    \Gamma{\left(z \right)}



.. code:: ipython3

    combsimp(gamma(x)*gamma(1 - x)) #ガンマ関数にも使える




.. math::

    \frac{\pi}{\sin{\left (\pi x \right )}}



5.6.4 一般化された超幾何関数
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    hyper([1, 2], [3], z)




.. math::

    {{}_{2}F_{1}\left(\begin{matrix} 1, 2 \\ 3 \end{matrix}\middle| {z} \right)}



5.6.5 関数を別の関数で書き換える
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    tan(x).rewrite(sin) #tanをsinで書き換える




.. math::

    \frac{2 \sin^{2}{\left (x \right )}}{\sin{\left (2 x \right )}}



.. code:: ipython3

    factorial(x).rewrite(gamma) #階乗をガンマ関数で書き換える




.. math::

    \Gamma{\left(x + 1 \right)}



5.6.6 特殊関数をいくつかの恒等式で書き換える
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    expand_func(gamma(x + 3))




.. math::

    x \left(x + 1\right) \left(x + 2\right) \Gamma{\left(x \right)}


