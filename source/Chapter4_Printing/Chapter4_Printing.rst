
Chapter 4 Printing
==================

Sympyではキレイに出力することができる！

詳しい内容については:
http://docs.sympy.org/latest/tutorial/printing.html 参照.

4.1 'Pretty Printing'の設定
---------------------------

.. code:: ipython3

    from sympy import init_printing

.. code:: ipython3

    init_printing()

--->自分の環境で最善な出力(``LaTeX``, ``MathML``\ など)を自動的に選ぶ.

.. code:: ipython3

    from sympy import init_session

.. code:: ipython3

    init_session() #前章参照


.. parsed-literal::

    IPython console for SymPy 1.0 (Python 3.6.0-64-bit) (ground types: python)
    
    These commands were executed:
    >>> from __future__ import division
    >>> from sympy import *
    >>> x, y, z, t = symbols('x y z t')
    >>> k, m, n = symbols('k m n', integer=True)
    >>> f, g, h = symbols('f g h', cls=Function)
    >>> init_printing()
    
    Documentation can be found at http://docs.sympy.org/1.0/


コンソールで実行すると\ ``LaTeX``\ 等で出力され、\ ``jupyter notebook``\ では

.. code:: ipython3

    from sympy import *
    x, y, z = symbols('x y z')
    init_printing()

.. code:: ipython3

    Integral(sqrt(1/x), x)




.. math::

    \int \sqrt{\frac{1}{x}}\, dx



とキレイに出力される！

latexで出力させたいときは

.. code:: ipython3

    print(latex(Integral(sqrt(1/x), x)))


.. parsed-literal::

    \int \sqrt{\frac{1}{x}}\, dx


とすればよい.(ちゃんと被積分関数とdxの間に,が入ってるのがすごい.)
