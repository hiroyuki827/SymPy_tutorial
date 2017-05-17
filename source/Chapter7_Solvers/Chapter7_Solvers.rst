
Chapter 7 Solvers
=================

.. code:: ipython3

    from sympy import *
    x, y, z = symbols('x y z')
    init_printing(use_unicode=True)

7.1 方程式についての注意
------------------------

``Sympy``\ での方程式は\ ``Eq``\ 関数を使う

.. code:: ipython3

    Eq(x,y)




.. math::

    x = y



.. code:: ipython3

    solveset(Eq(x**2, 1), x) #Eq(左辺, 右辺)




.. math::

    \left\{-1, 1\right\}



.. code:: ipython3

    solveset(Eq(x**2 - 1, 0), x)




.. math::

    \left\{-1, 1\right\}



.. code:: ipython3

    solveset(x**2 - 1, x) #式 = 0　とした「式」が第一引数




.. math::

    \left\{-1, 1\right\}



--->``Sympy``\ では、式はすべて\ ``=0``\ とみなされる.
``Eq``\ 関数を使わなくても、差で表現してやればよい.

7.2 代数的に方程式を解く
------------------------

代数方程式を解く基本的な関数は\ ``solveset(equation, variable=None, domain=S.Complexes)``.

他に関数\ ``solve()``\ があるが、基本的には関数\ ``solveset()``\ を用いる.

.. code:: ipython3

    solveset(x**2-x, x)




.. math::

    \left\{0, 1\right\}



.. code:: ipython3

    solveset(x - x, x, domain=S.Reals)




.. math::

    \mathbb{R}



.. code:: ipython3

    solveset(sin(x) - 1, x, domain=S.Reals)




.. math::

    \left\{2 n \pi + \frac{\pi}{2}\; |\; n \in \mathbb{Z}\right\}



--->勝手に``n``\ を導入して解いてくれる.

解がないときは空集合を返す:
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    solveset(exp(x),x)




.. math::

    \emptyset



.. code:: ipython3

    solveset(cos(x) - x, x)




.. math::

    \left\{x\; |\; x \in \mathbb{C} \wedge - x + \cos{\left (x \right )} = 0 \right\}



連立方程式: ``linsolve関数``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    linsolve([x + y + z - 1, x + y + 2*z -3], (x, y, z))




.. math::

    \left\{\left ( - y - 1, \quad y, \quad 2\right )\right\}



.. code:: ipython3

    linsolve(Matrix((
        [1, 1, 1, 1],
        [1, 1, 2, 3])), (x, y, z)) #Matrix関数についてはChapter 8




.. math::

    \left\{\left ( - y - 1, \quad y, \quad 2\right )\right\}



.. code:: ipython3

    M = Matrix(((
    1, 1, 1, 1),
     (1, 1, 2, 3)   
    ))

.. code:: ipython3

    system = A, b = M[:,:-1], M[:,-1]

.. code:: ipython3

    linsolve(system, x, y, z)




.. math::

    \left\{\left ( - y - 1, \quad y, \quad 2\right )\right\}



ただし高次の式になると,

.. code:: ipython3

    solveset(x**3 - 6*x**2 + 9*x, x)




.. math::

    \left\{0, 3\right\}



となって、解の重根の有無と縮退度がわからない. そこで

.. code:: ipython3

    roots(x**3 - 6*x**2 + 9*x, x)




.. math::

    \left \{ 0 : 1, \quad 3 : 2\right \}



とすれば、解\ ``0``\ が1個、解\ ``3``\ が2個出てくるとわかる.　実際

.. code:: ipython3

    factor(x**3 - 6*x**2 + 9*x)




.. math::

    x \left(x - 3\right)^{2}



**注意**: ``solveset``\ の使えない方程式 - 非線形多変数系 -
LambertWで解ける方程式

こういう場合は\ ``solve()``\ 関数で解ける:

.. code:: ipython3

    solve([x*y - 1, x -2], x, y)




.. math::

    \left [ \left ( 2, \quad \frac{1}{2}\right )\right ]



オプション\ ``dict ='True'``\ を付けると結果がリストの要素としての辞書型で得られる.

.. code:: ipython3

    solve([x*y - 1, x -2], x, y, dict ='True')




.. math::

    \left [ \left \{ x : 2, \quad y : \frac{1}{2}\right \}\right ]



.. code:: ipython3

    solve(x*exp(x) - 1, x)




.. math::

    \left [ \operatorname{LambertW}{\left (1 \right )}\right ]



7.3 微分方程式を解く
--------------------

関数の設定: SymPyオブジェクトへ。

.. code:: ipython3

    f, g = symbols('f g', cls=Function) #解きたいものが関数なので、cls=Function オプションを付ける.

.. code:: ipython3

    f(x)




.. math::

    f{\left (x \right )}



☆導関数は、Chapter 6
でやったように、関数Derivative()を使ってもできるし、

.. code:: ipython3

    Derivative(f(x),x,1)




.. math::

    \frac{d}{d x} f{\left (x \right )}



未知関数 ``f(x)`` に対するメソッド ``diff`` を用いても良い:

.. code:: ipython3

    f(x).diff(x)




.. math::

    \frac{d}{d x} f{\left (x \right )}



**コメント**

ここでdiffメソッドが使えたのは、未知関数f(x)に対して用いているため。

==> 今、微分方程式\ 

.. math:: f''(x)-2f'(x)+f(x)=\sin(x)

\ を解くことを考える. これは

.. code:: ipython3

    diffeq = Eq(f(x).diff(x, 2) - 2*f(x).diff(x) + f(x),  sin(x))

.. code:: ipython3

    diffeq #微分方程式




.. math::

    f{\left (x \right )} - 2 \frac{d}{d x} f{\left (x \right )} + \frac{d^{2}}{d x^{2}}  f{\left (x \right )} = \sin{\left (x \right )}



微分方程式は\ ``dsolve``\ 関数で解ける！

.. code:: ipython3

    dsolve(diffeq, f(x)) #第二引数で解きたい関数を指定




.. math::

    f{\left (x \right )} = \left(C_{1} + C_{2} x\right) e^{x} + \frac{1}{2} \cos{\left (x \right )}



.. code:: ipython3

    dsolve(f(x).diff(x)*(1-sin(f(x))),f(x))




.. math::

    f{\left (x \right )} + \cos{\left (f{\left (x \right )} \right )} = C_{1}


