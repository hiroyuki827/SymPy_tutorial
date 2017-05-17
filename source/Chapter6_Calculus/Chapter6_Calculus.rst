
Chapter 6 Calculus
==================

.. code:: ipython3

    from sympy import *
    x, y, z = symbols('x y z')
    init_printing(use_unicode=True)

6.1 微分
--------

微分するには

.. code:: ipython3

    diff(cos(x),x)




.. math::

    - \sin{\left (x \right )}



.. code:: ipython3

    diff(exp(x**2),x)




.. math::

    2 x e^{x^{2}}



とする。これらの関数はその場で微分操作を評価するので、未知関数には使えない。(微分方程式については後述)。

2階以上の微分をするには

.. code:: ipython3

    diff(x**4, x, x, x)




.. math::

    24 x



もしくは

.. code:: ipython3

    diff(x**4, x, 3) #xの3階微分.




.. math::

    24 x



とすればよい.

予想されるように、微分不可能になると

.. code:: ipython3

    diff(x**4, x, 5)




.. math::

    0



と0を返す.

多変数関数、たとえば\ 

.. math:: e^{xyz}

\ に対しても同様に

.. code:: ipython3

    expr = exp(x*y*z)

.. code:: ipython3

    diff(expr,x, y, y, z, z, z, z)




.. math::

    x^{3} y^{2} \left(x^{3} y^{3} z^{3} + 14 x^{2} y^{2} z^{2} + 52 x y z + 48\right) e^{x y z}



.. code:: ipython3

    diff(expr, x, y, 2, z, 4) #x,y:2階, z: 4階




.. math::

    x^{3} y^{2} \left(x^{3} y^{3} z^{3} + 14 x^{2} y^{2} z^{2} + 52 x y z + 48\right) e^{x y z}



.. code:: ipython3

    diff(expr, x, y, y, z, 4) #x, y, z: 4階




.. math::

    x^{3} y^{2} \left(x^{3} y^{3} z^{3} + 14 x^{2} y^{2} z^{2} + 52 x y z + 48\right) e^{x y z}



**コメント**
``diff``\ は\ ``Sympy``\ オブジェクトに対するメソッドとしても呼べる:

.. code:: ipython3

    expr.diff(x, y, y, z, 4)




.. math::

    x^{3} y^{2} \left(x^{3} y^{3} z^{3} + 14 x^{2} y^{2} z^{2} + 52 x y z + 48\right) e^{x y z}



微分操作を行いたくないとき
^^^^^^^^^^^^^^^^^^^^^^^^^^

上で述べたように、\ ``diff``\ 関数はその場で式を微分してしまうので、微分方程式には使えない.
その代わりに評価する前の微分を作るには\ ``Derivative``\ を使えばよい。

.. code:: ipython3

    deriv = Derivative(expr, x, y, y, z, 4)

.. code:: ipython3

    deriv




.. math::

    \frac{\partial^{7}}{\partial x\partial y^{2}\partial z^{4}}  e^{x y z}



これを評価するには\ ``doit``\ メソッドを使う:

.. code:: ipython3

    deriv.doit() #diff関数で評価するものと同じ.




.. math::

    x^{3} y^{2} \left(x^{3} y^{3} z^{3} + 14 x^{2} y^{2} z^{2} + 52 x y z + 48\right) e^{x y z}



**コメント**: ``Derivative()``\ は微分方程式などに使える.

6.2 積分
--------

.. code:: ipython3

    integrate(cos(x), x)




.. math::

    \sin{\left (x \right )}



**注意**: 不定積分であっても、未定定数は出力されない.

**例**: 定積分\ 

.. math:: \int_{0}^{\infty}e^{-x} \, dx

\ は

.. code:: ipython3

    integrate(exp(-x), (x, 0, oo)) #無限大は'o'２つ




.. math::

    1



と計算できる.

**例:** 2重積分\ 

.. math:: \int_{-\infty}^{\infty}\int_{-\infty}^{\infty}e^{-x^2-y^2}\, dx dy

\ は

.. code:: ipython3

    integrate(exp(-x**2-y**2), (x, -oo, oo), (y, -oo, oo))




.. math::

    \pi



と計算できる.

積分が評価できないとき
^^^^^^^^^^^^^^^^^^^^^^

もし積分が評価できないときは, ``Integral``\ で返す:

.. code:: ipython3

    expr = integrate(x**x, x)

.. code:: ipython3

    print(expr)


.. parsed-literal::

    Integral(x**x, x)


.. code:: ipython3

    expr




.. math::

    \int x^{x}\, dx



**コメント** 積分方程式に使える.

Integralの表式から積分値を評価したいとき
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    expr = Integral(log(x)**2, x)

.. code:: ipython3

    expr




.. math::

    \int \log^{2}{\left (x \right )}\, dx



.. code:: ipython3

    expr.doit() #doit()をつければ評価される.




.. math::

    x \log^{2}{\left (x \right )} - 2 x \log{\left (x \right )} + 2 x



**コメント**: ``integrate()``\ は合成関数とか、特殊関数に対しても使える!

.. code:: ipython3

    integ = Integral(x**y*exp(-x), (x, 0, oo))

.. code:: ipython3

    integ




.. math::

    \int_{0}^{\infty} x^{y} e^{- x}\, dx



.. code:: ipython3

    integ.doit()




.. math::

    \begin{cases} \Gamma{\left(y + 1 \right)} & \text{for}\: - \Re{y} < 1 \\\int_{0}^{\infty} x^{y} e^{- x}\, dx & \text{otherwise} \end{cases}



収束性の場合分けまでやってくれる!!!

6.3 極限
--------

極限\ 

.. math:: \lim_{x \rightarrow 0}\frac{\sin(x)}{x}

\ は

.. code:: ipython3

    limit(sin(x)/x, x, 0)




.. math::

    1



と評価できる.

.. code:: ipython3

    expr = x**2 / exp(x)

.. code:: ipython3

    expr.subs(x, oo) #oo/ooになる.




.. math::

    \mathrm{NaN}



.. code:: ipython3

    limit(expr, x, oo)




.. math::

    0



*極限操作と代入操作は厳密に区別すべき！*

極限を評価したくないとき
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    expr = Limit((cos(x) - 1)/x, x, 0)

.. code:: ipython3

    expr




.. math::

    \lim_{x \to 0^+}\left(\frac{1}{x} \left(\cos{\left (x \right )} - 1\right)\right)



.. code:: ipython3

    expr.doit() #極限値を評価




.. math::

    0



極限の方向を決めたいとき(正方向, 負方向)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. math:: \lim_{x\rightarrow 0^{+}}\frac{1}{x}

.. code:: ipython3

    limit(1/x, x, 0, '+') #第四引数に'+'のオプション




.. math::

    \infty



.. math:: \lim_{x \rightarrow 0^{-}}\frac{1}{x}

.. code:: ipython3

    limit(1/x, x, 0, '-') #第四引数に'-'のオプション




.. math::

    -\infty



6.4 べき級数
------------

関数\ ``f(x)``\ を\ ``x0``\ 周りで\ ``n``\ 次まで展開:
``f(x).series(x, x0, n)`` (``x0``, ``n``\ は省ける)

.. code:: ipython3

    expr = exp(sin(x))

.. code:: ipython3

    expr.series(x, 0, 4) #最高次はオーダー記号として出力




.. math::

    1 + x + \frac{x^{2}}{2} + \mathcal{O}\left(x^{4}\right)



.. code:: ipython3

    x + x**3 + x**6 + O(x**4) #大文字のO(オー)でオーダー記号を表せる.




.. math::

    x + x^{3} + \mathcal{O}\left(x^{4}\right)



.. code:: ipython3

    x*O(1) 




.. math::

    \mathcal{O}\left(x\right)



**コメント** オーダータームを除去するには

.. code:: ipython3

    expr.series(x, 0, 4).removeO() #こちらも大文字のO(オー)




.. math::

    \frac{x^{2}}{2} + x + 1



とすればよい. なお、オーダータームは原点以外の点の周りの展開でも現れる.

.. code:: ipython3

    exp(x - 6).series(x, x0=6) #パラメータ引数?




.. math::

    -5 + \frac{1}{2} \left(x - 6\right)^{2} + \frac{1}{6} \left(x - 6\right)^{3} + \frac{1}{24} \left(x - 6\right)^{4} + \frac{1}{120} \left(x - 6\right)^{5} + x + \mathcal{O}\left(\left(x - 6\right)^{6}; x\rightarrow6\right)



6.5 有限差分
------------

省略 http://docs.sympy.org/latest/tutorial/calculus.html#derivatives参照
