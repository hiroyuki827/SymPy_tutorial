
Chapter 2 Gotchas
=================

Chapter1で簡単に述べたように、\ ``SymPy``\ は\ ``Python``\ のライブラリとして提供されている。そのため\ **``Python``\ の構文規則は全く同様に使える**\ 。

例えば、四則演算\ ``+``, ``-``, ``*``, ``/``, ``//``, ``%``
は\ ``SymPy``\ でも同様に使える。

**コメント**:
チュートリアル原文では、SymPyで扱う変数を\ *SymPyシンボル*\ と呼んでいます。Pythonではシンボルに対応する概念がないのですが、チュートリアルに従っています。\ *SymPyオブジェクト*\ と呼んだときは、\ *SymPy*\ で定義されている関数・メソッドや、\ *SymPy*\ シンボル等を含めて総称として呼ぶことにします。

2.1 Symbols
~~~~~~~~~~~

.. code:: ipython3

    from sympy import * #関数やクラスをすべてインポートする.
    init_printing() #表示をキレイにするため、

.. code:: ipython3

    x + 1


::


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-2-4cf92658b648> in <module>()
    ----> 1 x + 1
    

    NameError: name 'x' is not defined


---> Python(オブジェクト)では「変数」は意味を持たない.
値を代入して初めて意味を持つ. その意味で

::

    NameError: name 'x' is not defined

が出ている。

**注意**
``sympy``\ で変数として扱いたい文字は、\ ``SymPy``\ オブジェクトに変換するために\ ``symbols``\ で定義しないといけない:

.. code:: ipython3

    x = symbols('x') #引数は文字列(str)

.. code:: ipython3

    type(x)




.. parsed-literal::

    sympy.core.symbol.Symbol



*コメント*:
一つの変数を定義するときも、\ ``symbols()``\ が使える。1変数用に\ ``Symbols``\ が定義されているが、必要ないかも?

複数の変数を定義する
''''''''''''''''''''

.. code:: ipython3

    x, y, z = symbols('x, y, z') #引数はスペースだけでも良い.

このようにして定義された変数は、(Pythonの変数とは違い)変数それ自体として意味を持つ.
すなわち

.. code:: ipython3

    a, b = symbols('a, b')

.. code:: ipython3

    a




.. math::

    a



.. code:: ipython3

    type(a) #aはSymPyオブジェクト




.. parsed-literal::

    sympy.core.symbol.Symbol



.. code:: ipython3

    b




.. math::

    b



となって、出力結果が文字\ ``a``, ``b``\ で表現されている。

ところで上の定義の\ ``a``, ``b``\ を入れ替えて

.. code:: ipython3

    a, b = symbols('b a') #左辺: Sympyシンボル 右辺:pythonオブジェクト

とすると、

.. code:: ipython3

    a




.. math::

    b



.. code:: ipython3

    b




.. math::

    a



と出力される。

これは何を意味するか?
``a``\ と名付けられたPython変数(左辺)は、\ ``Sympy``\ シンボルの\ ``b``\ (右辺)に対応している(残りも同様).
これは非常に混乱しやすいので、通常は名前をそれぞれ一致させるようにする.

**例外**:

-  Sympyのシンボル名がPythonの予約語を含んでいるとき
-  シンボル名(=Sympyの変数)がとても長く、短いPython変数で表したいとき

Python変数とSympyシンボルの違い
'''''''''''''''''''''''''''''''

次に代入操作の違いについて考える.

.. code:: ipython3

    x = symbols('x')

.. code:: ipython3

    expr = x + 1 #xはSympyシンボル

このSymPyシンボル\ ``x``\ に対して、\ ``Python``\ でよく知られた以下の代入操作を行うとどうなるだろうか？

.. code:: ipython3

    x = 2

.. code:: ipython3

    print(expr)


.. parsed-literal::

    x + 1


``expr = 3``\ が出力されない！しかしながら\ ``x``\ は値\ ``2``\ が代入されている.

.. code:: ipython3

    x




.. math::

    2



*コメント*
``expr``\ は\ ``SymPy``\ シンボル\ ``x``\ で定義されているが、代入操作\ ``x = 2``\ はそれの定義\ **後**\ に行われている.
そのため\ ``expr``\ は変更を受けない。

もしこの代入操作を反映した\ ``expr``\ がほしいなら、以下のように\ **``expr``\ を定義する前に**\ 代入操作を行う必要がある.

.. code:: ipython3

    x = symbols('x')

.. code:: ipython3

    x = 2

.. code:: ipython3

    expr = x + 1

.. code:: ipython3

    expr




.. math::

    3



.. code:: ipython3

    x




.. math::

    2



以上の操作は文字列に対しても同様に考えることができる.

**例**

.. code:: ipython3

    x = 'abc'

.. code:: ipython3

    expr = x + 'def'

.. code:: ipython3

    expr




.. parsed-literal::

    'abcdef'



.. code:: ipython3

    x = 'ABC' #exprの定義後に代入

.. code:: ipython3

    expr #'ABCdef'にならない.




.. parsed-literal::

    'abcdef'



**Q.**
では、プログラムの中で\ ``x``\ が変更を受けた後、再度exprの値を評価するにはどうすればよいか？

.. code:: ipython3

    x = symbols('x')

.. code:: ipython3

    expr = x + 1

.. code:: ipython3

    expr.subs({x:2}) #代入操作(多変数への代入があるとき)




.. math::

    3



.. code:: ipython3

    expr.subs(x,2) #代入操作(一変数への代入のとき)




.. math::

    3



2.4 等号
~~~~~~~~

等号\ ``=``, 真偽判定\ ``==``
もPythonと同様にして使えるが、数式評価には注意が必要

.. code:: ipython3

    x + 1 == 4




.. parsed-literal::

    False



--->日常でよく用いられる数式としてのイコール(``=``)とは違う!?
(``Python``\ では論理判定として使われている)

つまり、\ *通常私たちはa=bか判定するのは、Pythonで\ ``a == b``\ を行っているのと同じ.*

等式を表すには、\ ``Eq``\ 関数を使う

.. code:: ipython3

    Eq(x + 1, 4)




.. math::

    x + 1 = 4



[等式判定] **Q.** たとえば

.. math:: (x+1)^2=x^2+2x+1

\ が正しいかどうか知りたいとき、上で用いた==は使えるだろうか？

.. code:: ipython3

    (x + 1)**2 == x**2 + 2*x + 1 #使えない！




.. parsed-literal::

    False



ここで、==は両辺の表式が完全に等しいか聞いている。\ **記号として等しくないと**\ ``True``\ 判定にならない。
---> 括弧の位置、変数の位置諸々異なる。

「\ ``a == b`` を調べること「 (同じ) 「\ ``a = b`` を調べること」 (同じ)
「\ ``a - b = 0``\ を調べること」

===> つまり、数式評価を行うには\ ``a - b = 0``\ の形で行うのがベスト。

その上で、\ ``Eq``\ 関数を使うときは、\ ``(右辺)=0``\ となるような式の左辺を第一引数に入れるようにする.

式変形は\ ``simplify()``\ 関数で実行可能(詳しくは次章以降)

.. code:: ipython3

    a = (x + 1)**2

.. code:: ipython3

    b = x**2 + 2*x + 1

.. code:: ipython3

    simplify(a - b) #a - b が 0 に等しくなることをチェック.




.. math::

    a - b



.. code:: ipython3

    c = x**2 - 2*x + 1

.. code:: ipython3

    simplify(a - c)




.. math::

    a - x^{2} + 2 x - 1



任意の点で等しいかどうか調べる方法
''''''''''''''''''''''''''''''''''

.. code:: ipython3

    a = cos(x)**2 - sin(x)**2

.. code:: ipython3

    b = cos(2*x)

.. code:: ipython3

    a.equals(b) #equalsメソッドを使う.




.. parsed-literal::

    True



2.5 ^ と /
~~~~~~~~~~

``^`` : or, ``/`` : and (論理判定)

.. code:: ipython3

    True ^ False




.. parsed-literal::

    True



.. code:: ipython3

    True ^ True




.. parsed-literal::

    False



.. code:: ipython3

    x ^ y 




.. math::

    x \veebar y



**注意**: べき乗として\ ``^``\ を使ってはいけない.

コメント
''''''''

-  ``Sympy``\ オブジェクト + ``Sympy``\ オブジェクト
-  ``Sympy``\ オブジェクト + ``Python``\ オブジェクト

--> これらすべてSympyオブジェクトを与える.

.. code:: ipython3

    type(Integer(1) + 1) #Sympyオブジェクト+ Pythonオブジェクト




.. parsed-literal::

    sympy.core.numbers.Integer



.. code:: ipython3

    type(1 + 1) #Pythonオブジェクト + Pythonオブジェクト = Pythonオブジェクト




.. parsed-literal::

    int



注意 : 割り算
'''''''''''''

**注意**:
SymPyオブジェクトとしての整数型\ ``Integer()``\ 同士の割り算は有理数を与える。
(浮動小数点数でない。)

.. code:: ipython3

    Integer(1) / Integer (3) #SymPy: 有理数




.. math::

    \frac{1}{3}



.. code:: ipython3

    type(Integer(1) / Integer (3))




.. parsed-literal::

    sympy.core.numbers.Rational



.. code:: ipython3

    1 / 3 #Python: 浮動小数点数




.. math::

    0.3333333333333333



**注意**

-  ``Python2``\ では、整数型同士の割り算は小数点切り捨てになる

::

    >> 1 / 3 = 0

-  また以下を付けたときは、\ ``Python2``\ でも浮動小数点数になる.

::

    from __future__ import division

``Python``\ での計算なら、上のように浮動小数点数で得られる.

---> 上でまとめたように、\ ``Python``\ のバージョンで
``/``\ の役割が異なるので、\ ``SymPy``\ で割り算を表したいときは\ ``Rational``\ 関数を用いると良い。

.. code:: ipython3

    Rational(1,3) 




.. math::

    \frac{1}{3}



.. code:: ipython3

    type(Rational(1,3))




.. parsed-literal::

    sympy.core.numbers.Rational



のように定義する。このようにすれば

.. code:: ipython3

    x + Rational(1,3)




.. math::

    x + \frac{1}{3}



.. code:: ipython3

    type(x + Rational(1,3))




.. parsed-literal::

    sympy.core.add.Add



とおける。

このあたりの注意については
http://docs.sympy.org/latest/gotchas.html#gotchas を読む。
