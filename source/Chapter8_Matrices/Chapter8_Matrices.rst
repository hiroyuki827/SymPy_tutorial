
Chapter 8 Matrices
==================

行列に関してはコメントは少なめです。(\ [STRIKEOUT:正直、SympyのMatrix使うぐらいなら、Numpy使ったほうが効率良さそうだよね〜.])

.. code:: ipython3

    from sympy import *
    init_printing(use_unicode=True)

``Sympy``\ で行列を作るには、\ ``Matrix``\ オブジェクトを使う. たとえば

.. code:: ipython3

    Matrix([
    [1, -1],
    [3, 4],
    [0, 2] ]) #行ベクトルの組み合わせであることを明示するために[]カッコを余分に付けている。




.. math::

    \left[\begin{matrix}1 & -1\\3 & 4\\0 & 2\end{matrix}\right]



とできる. 列ベクトルは

.. code:: ipython3

    Matrix([1,2,3]) #([])はベクトル




.. math::

    \left[\begin{matrix}1\\2\\3\end{matrix}\right]



行列の積
^^^^^^^^

.. code:: ipython3

    M = Matrix([
    [1, 2, 3],
    [3, 2, 1] ])

.. code:: ipython3

    type(M)




.. parsed-literal::

    sympy.matrices.dense.MutableDenseMatrix



.. code:: ipython3

    N = Matrix([0, 1, 1])

.. code:: ipython3

    M*N




.. math::

    \left[\begin{matrix}5\\3\end{matrix}\right]



**注意**: ``Matrix``\ オブジェクトは\ ``mutable``.

8.1　基本的な演算
-----------------

8.1.1 形(Shape)
~~~~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
     [1, 2, 3],
     [-2, 0, 4]
    ])

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}1 & 2 & 3\\-2 & 0 & 4\end{matrix}\right]



.. code:: ipython3

    M.shape #Numpy同様()はいらない.




.. math::

    \left ( 2, \quad 3\right )



(行, 列)

8.1.2 行と列を参照する
~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    M.row(0) #1行 0から始まる.




.. math::

    \left[\begin{matrix}1 & 2 & 3\end{matrix}\right]



.. code:: ipython3

    M.col(-1) #3列(負で最終列を参照)




.. math::

    \left[\begin{matrix}3\\4\end{matrix}\right]



8.1.2 行と列の削除 / 挿入
~~~~~~~~~~~~~~~~~~~~~~~~~

行 / 列の削除
^^^^^^^^^^^^^

.. code:: ipython3

    M.col_del(0) # 一列目を削除

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}2 & 3\\0 & 4\end{matrix}\right]



.. code:: ipython3

    M.row_del(1) # 二行目を削除

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}2 & 3\end{matrix}\right]



行 /　列の追加
^^^^^^^^^^^^^^

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}2 & 3\end{matrix}\right]



.. code:: ipython3

    M = M.row_insert(1, Matrix([[0, 4]])) #2行目に(0, 4)成分を追加

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}2 & 3\\0 & 4\end{matrix}\right]



.. code:: ipython3

    M = M.col_insert(0, Matrix([1, -2])) #列ベクトルを追加するのでカッコは一組

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}1 & 2 & 3\\-2 & 0 & 4\end{matrix}\right]



8.2 基本的な方法
----------------

.. code:: ipython3

    M = Matrix([
        [1, 3],
        [-2,3]
    ])

.. code:: ipython3

    N = Matrix([
        [0,3],
        [0,7]
    ])

.. code:: ipython3

    M + N #足し算




.. math::

    \left[\begin{matrix}1 & 6\\-2 & 10\end{matrix}\right]



.. code:: ipython3

    M*N #行列の積




.. math::

    \left[\begin{matrix}0 & 24\\0 & 15\end{matrix}\right]



.. code:: ipython3

    3*M #定数倍




.. math::

    \left[\begin{matrix}3 & 9\\-6 & 9\end{matrix}\right]



.. code:: ipython3

    M**2 #べき乗




.. math::

    \left[\begin{matrix}-5 & 12\\-8 & 3\end{matrix}\right]



.. code:: ipython3

    M**-1 #逆行列




.. math::

    \left[\begin{matrix}\frac{1}{3} & - \frac{1}{3}\\\frac{2}{9} & \frac{1}{9}\end{matrix}\right]



.. code:: ipython3

    N**-1 #行列式がゼロで存在しない.


::


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-59-4c604f092c13> in <module>()
    ----> 1 N**-1 #行列式がゼロで存在しない.
    

    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/core/decorators.py in binary_op_wrapper(self, other)
        116                     else:
        117                         return f(self)
    --> 118             return func(self, other)
        119         return binary_op_wrapper
        120     return priority_decorator


    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/matrices/dense.py in __pow__(self, other)
        577     @call_highest_priority('__rpow__')
        578     def __pow__(self, other):
    --> 579         return super(DenseMatrix, self).__pow__(other)
        580 
        581     @call_highest_priority('__pow__')


    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/matrices/matrices.py in __pow__(self, num)
        532             n = int(num)
        533             if n < 0:
    --> 534                 return self.inv()**-n   # A**-2 = (A**-1)**2
        535             a = eye(self.cols)
        536             s = self


    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/matrices/matrices.py in inv(self, method, **kwargs)
        311         if method is not None:
        312             kwargs['method'] = method
    --> 313         return self._eval_inverse(**kwargs)
        314 
        315     def inv_mod(self, m):


    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/matrices/dense.py in _eval_inverse(self, **kwargs)
        306         M = self.as_mutable()
        307         if method == "GE":
    --> 308             rv = M.inverse_GE(iszerofunc=iszerofunc)
        309         elif method == "LU":
        310             rv = M.inverse_LU(iszerofunc=iszerofunc)


    /usr/local/anaconda3/lib/python3.6/site-packages/sympy/matrices/matrices.py in inverse_GE(self, iszerofunc)
       2662         red = big.rref(iszerofunc=iszerofunc, simplify=True)[0]
       2663         if any(iszerofunc(red[j, j]) for j in range(red.rows)):
    -> 2664             raise ValueError("Matrix det == 0; not invertible.")
       2665 
       2666         return self._new(red[:, big.rows:])


    ValueError: Matrix det == 0; not invertible.


.. code:: ipython3

    M = Matrix([
        [1, 2, 3],
        [4, 5, 6]
    ])

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}1 & 2 & 3\\4 & 5 & 6\end{matrix}\right]



.. code:: ipython3

    M.T #転置行列




.. math::

    \left[\begin{matrix}1 & 4\\2 & 5\\3 & 6\end{matrix}\right]



8.3 行列の構成
--------------

8.3.1 単位行列
~~~~~~~~~~~~~~

.. code:: ipython3

    eye(3)




.. math::

    \left[\begin{matrix}1 & 0 & 0\\0 & 1 & 0\\0 & 0 & 1\end{matrix}\right]



.. code:: ipython3

    eye(4)




.. math::

    \left[\begin{matrix}1 & 0 & 0 & 0\\0 & 1 & 0 & 0\\0 & 0 & 1 & 0\\0 & 0 & 0 & 1\end{matrix}\right]



8.3.2 ゼロ行列
~~~~~~~~~~~~~~

.. code:: ipython3

    zeros(2,3)




.. math::

    \left[\begin{matrix}0 & 0 & 0\\0 & 0 & 0\end{matrix}\right]



.. code:: ipython3

    zeros(4)




.. math::

    \left[\begin{matrix}0 & 0 & 0 & 0\\0 & 0 & 0 & 0\\0 & 0 & 0 & 0\\0 & 0 & 0 & 0\end{matrix}\right]



8.3.3 すべての成分が1の行列
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    ones(2,3)




.. math::

    \left[\begin{matrix}1 & 1 & 1\\1 & 1 & 1\end{matrix}\right]



8.3.4 対角行列
~~~~~~~~~~~~~~

.. code:: ipython3

    diag(1, 2, 3)




.. math::

    \left[\begin{matrix}1 & 0 & 0\\0 & 2 & 0\\0 & 0 & 3\end{matrix}\right]



.. code:: ipython3

    diag(-1, ones(2, 2), Matrix([5, 7, 5])) #複数組み合わせる




.. math::

    \left[\begin{matrix}-1 & 0 & 0 & 0\\0 & 1 & 1 & 0\\0 & 1 & 1 & 0\\0 & 0 & 0 & 5\\0 & 0 & 0 & 7\\0 & 0 & 0 & 5\end{matrix}\right]



8.4 高等的な扱い
----------------

8.4.1 行列式
~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
        [1, 0, 1],
        [2, -1, 3],
        [4, 3, 2]
    ])

.. code:: ipython3

    M




.. math::

    \left[\begin{matrix}1 & 0 & 1\\2 & -1 & 3\\4 & 3 & 2\end{matrix}\right]



.. code:: ipython3

    M.det()




.. math::

    -1



8.4.2 簡約化
~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
        [1, 0, 1, 3],
        [2, 3, 4, 7],
        [-1, -3, -3, -4]
    ])

.. code:: ipython3

    M.rref()




.. math::

    \left ( \left[\begin{matrix}1 & 0 & 1 & 3\\0 & 1 & \frac{2}{3} & \frac{1}{3}\\0 & 0 & 0 & 0\end{matrix}\right], \quad \left [ 0, \quad 1\right ]\right )



第一引数は簡約化した行列、第二引数はピボット列の添字リスト.
この行列のランクは2.

8.4.3 ヌル空間
~~~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
        [1, 2, 3, 0, 0],
        [4, 10, 0, 0, 1]
    ])

.. code:: ipython3

    M.nullspace()




.. math::

    \left [ \left[\begin{matrix}-15\\6\\1\\0\\0\end{matrix}\right], \quad \left[\begin{matrix}0\\0\\0\\1\\0\end{matrix}\right], \quad \left[\begin{matrix}1\\- \frac{1}{2}\\0\\0\\1\end{matrix}\right]\right ]



--->M\*x=0なる方程式の解. ヌル空間の次元は3.

8.4.4 列空間
~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
        [1, 1, 2],
        [2, 1, 3],
        [3, 1, 4]
    ])

.. code:: ipython3

    M.columnspace()




.. math::

    \left [ \left[\begin{matrix}1\\2\\3\end{matrix}\right], \quad \left[\begin{matrix}1\\1\\1\end{matrix}\right]\right ]



8.4.5 固有値、固有ベクトル、対角化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: ipython3

    M = Matrix([
        [3, -2, 4, -2],
        [5, 3, -3, -2],
        [5, -2, 2, -2],
        [5, -2, -3 ,3]
    ])

.. code:: ipython3

    M.eigenvals()




.. math::

    \left \{ -2 : 1, \quad 3 : 1, \quad 5 : 2\right \}



--->固有値 -2 と 3 は多重度1, 固有値5は多重度 3

.. code:: ipython3

    M.eigenvects()




.. math::

    \left [ \left ( -2, \quad 1, \quad \left [ \left[\begin{matrix}0\\1\\1\\1\end{matrix}\right]\right ]\right ), \quad \left ( 3, \quad 1, \quad \left [ \left[\begin{matrix}1\\1\\1\\1\end{matrix}\right]\right ]\right ), \quad \left ( 5, \quad 2, \quad \left [ \left[\begin{matrix}1\\1\\1\\0\end{matrix}\right], \quad \left[\begin{matrix}0\\-1\\0\\1\end{matrix}\right]\right ]\right )\right ]



--->固有値とともに固有ベクトルも表示される.
常にコレを使ってもいいが、固有ベクトルの計算は時間がかかるので、固有値だけ欲しければeigenvals()を使うのが吉.

.. code:: ipython3

    P, D = M.diagonalize()

.. code:: ipython3

    P #直交行列




.. math::

    \left[\begin{matrix}0 & 1 & 1 & 0\\1 & 1 & 1 & -1\\1 & 1 & 1 & 0\\1 & 1 & 0 & 1\end{matrix}\right]



.. code:: ipython3

    D #対角行列




.. math::

    \left[\begin{matrix}-2 & 0 & 0 & 0\\0 & 3 & 0 & 0\\0 & 0 & 5 & 0\\0 & 0 & 0 & 5\end{matrix}\right]



.. code:: ipython3

    P*D*P**-1




.. math::

    \left[\begin{matrix}3 & -2 & 4 & -2\\5 & 3 & -3 & -2\\5 & -2 & 2 & -2\\5 & -2 & -3 & 3\end{matrix}\right]



これはMに等しい.

固有方程式
^^^^^^^^^^

.. code:: ipython3

    lamda = symbols('lamda') #lambdaは予約語

.. code:: ipython3

    p = M.charpoly(lamda)

.. code:: ipython3

    factor(p) #Mの固有方程式




.. math::

    \left(\lambda - 5\right)^{2} \left(\lambda - 3\right) \left(\lambda + 2\right)


