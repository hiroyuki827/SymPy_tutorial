
Chapter 3 基本的な演算
======================

.. code:: ipython3

    from sympy import *
    init_printing() #出力結果をキレイにするため
    x, y, z = symbols('x y z')

ここでは基本的な計算の方法を扱う.

**Tips**
これ以降変数の定義はチュートリアルに沿って行うが、以下のように宣言すれば、いちいち(\ ``symbols``\ を用いた変数宣言を)しなくて済む.

.. code:: ipython3

    from sympy import init_session
    init_session()


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


===> よく用いられる以下のものが自動的に読み込まれている.

-  ``from __future__ import division`` :
   ``Python2``\ で割り算が\ ``Python3``\ と同じものになる.
-  ``from sympy import *`` : すべてのSymPyオブジェクト(関数)をインポート
-  ``x, y, z, t`` : 変数
-  ``k, m, n`` : 整数(和の添字として)
-  ``f, g, h`` : 関数の名前
-  ``init_printing()`` : 表記を環境に応じて最適なものにする. jupyter
   notebook上ならLaTeXライクなものにする

3.1 代入
--------

*SymPy*\ シンボルに関する代入は前章でも扱ったが、式を定義する順番を気にする必要があった.
それを気にする必要がない方法として\ ``subs``\ 関数を用いる方法がある.

値を代入する
''''''''''''

.. code:: ipython3

    expr = cos(x) + 1

.. code:: ipython3

    expr.subs({x:y}) #subsメソッド: xにyを代入する.




.. math::

    \cos{\left (y \right )} + 1



.. code:: ipython3

    expr.subs(x, y) 




.. math::

    \cos{\left (y \right )} + 1



``y = 0``\ (つまり ``x = 0``) での評価は

.. code:: ipython3

    expr.subs(x, 0)




.. math::

    2



式を代入する
''''''''''''

また\ 

.. math:: x^{x^{x^{x}}}

のようなものはどうすればよいか. --->
``x``\ として、\ ``y``\ を\ ``x**y``\ で置き換えてやれば良い.

.. code:: ipython3

    expr = x**y

.. code:: ipython3

    expr




.. math::

    x^{y}



.. code:: ipython3

    expr = expr.subs(y, x**y)

.. code:: ipython3

    expr




.. math::

    x^{x^{y}}



.. code:: ipython3

    expr = expr.subs(y, x**x)

.. code:: ipython3

    expr




.. math::

    x^{x^{x^{x}}}



特定の関数・変数のみで書き換えたい
''''''''''''''''''''''''''''''''''

.. code:: ipython3

    expr = sin(2*x) + cos(2*x)

.. code:: ipython3

    expand_trig(expr) #expand_trig関数を使って三角関数を展開




.. math::

    2 \sin{\left (x \right )} \cos{\left (x \right )} + 2 \cos^{2}{\left (x \right )} - 1



.. code:: ipython3

    expr.subs(sin(2*x), 2*sin(x)*cos(x)) #sin(2x)を2sin(x)cos(x)に置き換える.




.. math::

    2 \sin{\left (x \right )} \cos{\left (x \right )} + \cos{\left (2 x \right )}



詳しい\ ``SymPy``\ 関数についてはChapter 5にて述べる.

**注意** *``Sympy``\ シンボルは\ ``immutable``*

.. code:: ipython3

    expr = cos(x)

.. code:: ipython3

    expr.subs(x, 0) #x=0をexprへ代入




.. math::

    2



.. code:: ipython3

    expr




.. math::

    \cos{\left (x \right )}



--->値を代入しても、最初の式``expr = cos(x)``\ は変更を受けていない.
(``Python``\ でのsort, sorted関数と同様に考えれば良い.)

つまり\ ``Sympy``\ での代入操作\ ``subs()``\ はオリジナルの値を変数へ入れるのではなく、\ **``expr``\ のコピー**\ に代入操作\ ``subs(x, 0)``\ を行い、出力していると考えれば良い。

.. code:: ipython3

    x




.. math::

    x



--->値``x = 0``\ を代入した変数だが、変更を受けていない.

幾つかの代入を一度に行う
^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: ipython3

    expr = x**3 + 4*x*y - z

.. code:: ipython3

    expr.subs([(x, 2), (y, 4), (z, 0)]) #引数はこのようにリストでも与えることができるし、




.. math::

    40



.. code:: ipython3

    expr.subs({x:2, y:4, z:0}) #辞書として与えることもできる.




.. math::

    40



代入したい変数がたくさんあるとき

[**例**]

.. math:: x^4 - 4x^3 + 4x^2 - 2x + 3

\ の\ *偶数べき*\ の\ ``x``\ を\ ``y``\ に置き換えたい:

.. code:: ipython3

    expr = x**4 - 4*x**3 + 4*x**2 - 2*x + 3

.. code:: ipython3

    replacements = [(x**i, y**i) for i in range(5) if i % 2 == 0]

.. code:: ipython3

    expr.subs(replacements)




.. math::

    - 4 x^{3} - 2 x + y^{4} + 4 y^{2} + 3



3.2 文字列をSympyの表式に変換する
---------------------------------

関数\ ``sympify()``\ は文字列を\ ``Sympy``\ オブジェクトに変換する。

たとえば\ ``input``\ 関数でユーザー入力を受け付けて式を変数\ ``str_expr``\ に代入したとすると、その時点では以下のように文字列で認識されている.

.. code:: ipython3

    str_expr = 'x**2 + 3*x - 1/2'

.. code:: ipython3

    type(str_expr)




.. parsed-literal::

    str



.. code:: ipython3

    str_expr




.. parsed-literal::

    'x**2 + 3*x - 1/2'



.. code:: ipython3

    str_expr.subs(x, 2) #str_exprは単なる文字列なので代入操作不可能


::


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-25-93c439777d80> in <module>()
    ----> 1 str_expr.subs(x, 2)
    

    AttributeError: 'str' object has no attribute 'subs'


.. code:: ipython3

    expr = sympify(str_expr) #Sympyオブジェクトに変換

.. code:: ipython3

    type(expr)




.. parsed-literal::

    sympy.core.add.Add



.. code:: ipython3

    expr




.. math::

    x^{2} + 3 x - \frac{1}{2}



.. code:: ipython3

    expr.subs(x, 2) #xがSympyオブジェクトとして扱えるようになり、代入操作も可能になる.




.. math::

    \frac{19}{2}



浮動小数点数に変換する
''''''''''''''''''''''

ここではこれまで扱ってきた数式の数値評価を考える.

.. code:: ipython3

    expr = sqrt(8)

.. code:: ipython3

    expr




.. math::

    2 \sqrt{2}



.. code:: ipython3

    expr.evalf() #evalf()メソッド




.. math::

    2.82842712474619



``evalf()``\ の引数には、欲しい桁を与えられる. デフォルトでは15桁.
100桁ほしいなら

.. code:: ipython3

    pi.evalf(10e2)




.. math::

    3.141592653589793238462643383279502884197169399375105820974944592307816406286208998628034825342117067982148086513282306647093844609550582231725359408128481117450284102701938521105559644622948954930381964428810975665933446128475648233786783165271201909145648566923460348610454326648213393607260249141273724587006606315588174881520920962829254091715364367892590360011330530548820466521384146951941511609433057270365759591953092186117381932611793105118548074462379962749567351885752724891227938183011949129833673362440656643086021394946395224737190702179860943702770539217176293176752384674818467669405132000568127145263560827785771342757789609173637178721468440901224953430146549585371050792279689258923542019956112129021960864034418159813629774771309960518707211349999998372978049951059731732816096318595024459455346908302642522308253344685035261931188171010003137838752886587533208381420617177669147303598253490428755468731159562863882353787593751957781857780532171226806613001927876611195909216420198



浮動小数点数が欲しい時は、\ ``subs``\ で表式に代入してから\ ``evalf``\ を使えば良い。
一つの式として表せば

.. code:: ipython3

    expr = cos(2*x)

.. code:: ipython3

    expr.evalf(subs={x:2.4})




.. math::

    0.0874989834394464



とすればよい。

*コメント* ``expr.evalf()``\ オブジェクトの引数に注意. 桁数ではない.)

*Tips*: 全体の精度に響かない値を、単に切り捨てたいときがある. たとえば

.. code:: ipython3

    one = cos(1)**2 + sin(1)**2

.. code:: ipython3

    (one - 1).evalf()




.. math::

    -4.0 \cdot 10^{-124}



なんかは、全体の精度に対してほとんど無視して良いといえる. そこで

.. code:: ipython3

    (one-1).evalf(chop=True)




.. math::

    0



と、\ ``chop``\ フラグをつければ無事小数点以下を切り捨てられる.

3.3 Lambdify
------------

たくさんの点で、式を評価したいときは、もっと簡単な方法がある.
-->数千個の点になってくると``Sympy``\ だと遅いので、\ ``Numpy``\ とか\ ``SciPy``\ を使うべき.

.. code:: ipython3

    help(lambdify)


.. parsed-literal::

    Help on function lambdify in module sympy.utilities.lambdify:
    
    lambdify(args, expr, modules=None, printer=None, use_imps=True, dummify=True)
        Returns a lambda function for fast calculation of numerical values.
        
        If not specified differently by the user, SymPy functions are replaced as
        far as possible by either python-math, numpy (if available) or mpmath
        functions - exactly in this order. To change this behavior, the "modules"
        argument can be used. It accepts:
        
         - the strings "math", "mpmath", "numpy", "numexpr", "sympy"
         - any modules (e.g. math)
         - dictionaries that map names of sympy functions to arbitrary functions
         - lists that contain a mix of the arguments above, with higher priority
           given to entries appearing first.
        
        The default behavior is to substitute all arguments in the provided
        expression with dummy symbols. This allows for applied functions (e.g.
        f(t)) to be supplied as arguments. Call the function with dummify=False if
        dummy substitution is unwanted (and `args` is not a string). If you want
        to view the lambdified function or provide "sympy" as the module, you
        should probably set dummify=False.
        
        For functions involving large array calculations, numexpr can provide a
        significant speedup over numpy.  Please note that the available functions
        for numexpr are more limited than numpy but can be expanded with
        implemented_function and user defined subclasses of Function.  If specified,
        numexpr may be the only option in modules. The official list of numexpr
        functions can be found at:
        https://github.com/pydata/numexpr#supported-functions
        
        In previous releases ``lambdify`` replaced ``Matrix`` with ``numpy.matrix``
        by default. As of release 1.0 ``numpy.array`` is the default.
        To get the old default behavior you must pass in ``[{'ImmutableMatrix':
        numpy.matrix}, 'numpy']`` to the ``modules`` kwarg.
        
        >>> from sympy import lambdify, Matrix
        >>> from sympy.abc import x, y
        >>> import numpy
        >>> array2mat = [{'ImmutableMatrix': numpy.matrix}, 'numpy']
        >>> f = lambdify((x, y), Matrix([x, y]), modules=array2mat)
        >>> f(1, 2)
        matrix([[1],
                [2]])
        
        Usage
        =====
        
        (1) Use one of the provided modules:
        
            >>> from sympy import sin, tan, gamma
            >>> from sympy.utilities.lambdify import lambdastr
            >>> from sympy.abc import x, y
            >>> f = lambdify(x, sin(x), "math")
        
            Attention: Functions that are not in the math module will throw a name
                       error when the lambda function is evaluated! So this would
                       be better:
        
            >>> f = lambdify(x, sin(x)*gamma(x), ("math", "mpmath", "sympy"))
        
        (2) Use some other module:
        
            >>> import numpy
            >>> f = lambdify((x,y), tan(x*y), numpy)
        
            Attention: There are naming differences between numpy and sympy. So if
                       you simply take the numpy module, e.g. sympy.atan will not be
                       translated to numpy.arctan. Use the modified module instead
                       by passing the string "numpy":
        
            >>> f = lambdify((x,y), tan(x*y), "numpy")
            >>> f(1, 2)
            -2.18503986326
            >>> from numpy import array
            >>> f(array([1, 2, 3]), array([2, 3, 5]))
            [-2.18503986 -0.29100619 -0.8559934 ]
        
        (3) Use a dictionary defining custom functions:
        
            >>> def my_cool_function(x): return 'sin(%s) is cool' % x
            >>> myfuncs = {"sin" : my_cool_function}
            >>> f = lambdify(x, sin(x), myfuncs); f(1)
            'sin(1) is cool'
        
        Examples
        ========
        
        >>> from sympy.utilities.lambdify import implemented_function
        >>> from sympy import sqrt, sin, Matrix
        >>> from sympy import Function
        >>> from sympy.abc import w, x, y, z
        
        >>> f = lambdify(x, x**2)
        >>> f(2)
        4
        >>> f = lambdify((x, y, z), [z, y, x])
        >>> f(1,2,3)
        [3, 2, 1]
        >>> f = lambdify(x, sqrt(x))
        >>> f(4)
        2.0
        >>> f = lambdify((x, y), sin(x*y)**2)
        >>> f(0, 5)
        0.0
        >>> row = lambdify((x, y), Matrix((x, x + y)).T, modules='sympy')
        >>> row(1, 2)
        Matrix([[1, 3]])
        
        Tuple arguments are handled and the lambdified function should
        be called with the same type of arguments as were used to create
        the function.:
        
        >>> f = lambdify((x, (y, z)), x + y)
        >>> f(1, (2, 4))
        3
        
        A more robust way of handling this is to always work with flattened
        arguments:
        
        >>> from sympy.utilities.iterables import flatten
        >>> args = w, (x, (y, z))
        >>> vals = 1, (2, (3, 4))
        >>> f = lambdify(flatten(args), w + x + y + z)
        >>> f(*flatten(vals))
        10
        
        Functions present in `expr` can also carry their own numerical
        implementations, in a callable attached to the ``_imp_``
        attribute.  Usually you attach this using the
        ``implemented_function`` factory:
        
        >>> f = implemented_function(Function('f'), lambda x: x+1)
        >>> func = lambdify(x, f(x))
        >>> func(4)
        5
        
        ``lambdify`` always prefers ``_imp_`` implementations to implementations
        in other namespaces, unless the ``use_imps`` input parameter is False.
    


Numpy
'''''

.. code:: ipython3

    import numpy #numpyを呼び出すためインポートする.

.. code:: ipython3

    a = numpy.arange(10) #Numpyでのリスト(10個)
    a




.. parsed-literal::

    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])



.. code:: ipython3

    f = lambdify(x, sin(x), 'numpy') #numpyをバックエンドで呼び出して計算させる. 第２引数に関数を持ってくる.

.. code:: ipython3

    f(a) #引数はnumpyオブジェクト




.. parsed-literal::

    array([ 0.        ,  0.84147098,  0.90929743,  0.14112001, -0.7568025 ,
           -0.95892427, -0.2794155 ,  0.6569866 ,  0.98935825,  0.41211849])



--->関数値が格納できている.

標準ライブラリmath
''''''''''''''''''

.. code:: ipython3

    f = lambdify(x, sin(x), 'math')

.. code:: ipython3

    f(0.1)




.. math::

    0.09983341664682815



自分で関数を定義する
''''''''''''''''''''

.. code:: ipython3

    def mysin(x):
        '''
        My sine. Note that this is only accurate for small x.
        '''
        return x

.. code:: ipython3

    f = lambdify(x, expr, {'sin':mysin})

.. code:: ipython3

    f(0.1)




.. math::

    0.1


