# GSoC 2021 Report - Improve and Expand Functionalities in Control Module

<p align="center">
<img src=https://user-images.githubusercontent.com/53227127/130247629-81e83ef8-0d05-4f08-8edf-233abf8a1acb.png width=140 height=140></img>ㅤㅤㅤ<img src=https://user-images.githubusercontent.com/53227127/130246696-f7188bb7-3708-4b84-8f46-cc1119b6eb76.png></img>
</p>

<h2>About Me</h2>

I am [Akshansh Bhatt](https://www.github.com/akshanshbhatt), a 2nd-year undergraduate student pursuing a dual major in Physics and Electronics Engineering at [BITS Pilani](https://bits-pilani.ac.in/). I was part of the Google Summer of Code 2021 program as a student developer at [SymPy](https://github.com/sympy/sympy). You can find my project [here](https://summerofcode.withgoogle.com/projects/#6135134387961856). For information related to my weekly progress in the program, you can check out my [blog posts](https://gsoc-blog.akshansh.me/). I am available for discussions/feedback on this [email address](mailto:qaz.akshansh@gmail.com).

<h2>Project Synopsis</h2>

My project aimed to achieve 3 main goals -

- Complete the unfinished PR(s) and fix bugs from [last year's work](https://github.com/namannimmo10/Google-Summer-of-Code-with-SymPy) by [@namannimmo10](https://www.github.com/namannimmo10).
- Make the control module's API more robust and beginner-friendly by adding examples related to use cases.
- Add functionalities in the control module that were proposed last year, but work didn't start. This mainly included `StateSpace` class and common plots related to control theory.

<h2>Work Accomplished</h2>

#### Major PRs

- [#21634](https://github.com/sympy/sympy/pull/21634) : Improve `TransferFunction` docs and add `to_expr`
- [#21653](https://github.com/sympy/sympy/pull/21653) : [GSoC] Add `TransferFunctionMatrix` class
- [#19761](https://github.com/sympy/sympy/pull/19761) : [GSoC] Add `TransferFunctionMatrix class` in `physics.control`
- [#21703](https://github.com/sympy/sympy/pull/21703) : Implement `MIMOSeries` and `MIMOParallel`
- [#21763](https://github.com/sympy/sympy/pull/21763) : Add graphical analyses in `physics.control`
- [#21833](https://github.com/sympy/sympy/pull/21833) : Implement `MIMOFeedback` class

#### Minor PRs

- [#21587](https://github.com/sympy/sympy/pull/21587) : fix(physics.control): order of classes in doc
- [#21820](https://github.com/sympy/sympy/pull/21820) : Update example in `MIMOParallel.var`

<h2>Work in Progress</h2>

- Adding Textbook examples demonstrating the use cases of the modules functionalities.

<h2>Examples</h2>

In chronological order of addition to the codebase, these are some examples demonstrating my work -

- `TransferFunction` instances from sympy `Expr` (expression class) using `from_rational_expression()` classmethod.
```py
>>> from sympy.abc import s, p, a
>>> from sympy.physics.control.lti import TransferFunction
>>> expr1 = s/(s**2 + 2*s + 1)
>>> tf1 = TransferFunction.from_rational_expression(expr1)
>>> tf1
TransferFunction(s, s**2 + 2*s + 1, s)
>>> expr2 = (a*p**3 - a*p**2 + s*p)/(p + a**2)  # Expr with more than one variables
>>> tf2 = TransferFunction.from_rational_expression(expr2, p)
>>> tf2
TransferFunction(a*p**3 - a*p**2 + p*s, a**2 + p, p)
```

- Get the equivalent rational expression by using the `to_expr()` method. This is helpful specially when the expression is to be manipulated rather than the `TransferFunction` object. It also works for `Series` and `Parallel` objects.
```py
>>> from sympy.abc import s, p, a, b
>>> from sympy.physics.control.lti import TransferFunction
>>> from sympy import Expr
>>> tf1 = TransferFunction(s, a*s**2 + 1, s)
>>> tf1.to_expr()
s/(a*s**2 + 1)
>>> isinstance(_, Expr)
True
>>> tf2 = TransferFunction(1, (p + 3*b)*(b - p), p)
>>> tf2.to_expr()
1/((b - p)*(3*b + p))
>>> tf3 = TransferFunction((s - 2)*(s - 3), (s - 1)*(s - 2)*(s - 3), s)
>>> tf3.to_expr()  # Makes sure that poles and zeros are not cancelled atomatically
((s - 3)*(s - 2))/(((s - 3)*(s - 2)*(s - 1)))
>>> _.simplify()  # Manipulate this expr however you like
1/(s - 1)
```

- `TransferFunctionMatrix` class for representing MIMO tf systems.
```py
>>> from sympy.abc import s
>>> from sympy import pprint
>>> from sympy.physics.control.lti import TransferFunction, TransferFunctionMatrix
>>> TF = TransferFunction; TFM = TransferFunctionMatrix
>>> tf_1 = TF(5, s, s)
>>> tf_2 = TF(5*s, (2 + s**2), s)
>>> tf_3 = TF(5, (s*(2 + s**2)), s)
>>> tf_4 = TF(10, s, s)
>>> H_1 = TFM([[tf_1, tf_2], [tf_3, tf_4]])
>>> H_1
TransferFunctionMatrix(((TransferFunction(5, s, s), TransferFunction(5*s, s**2 + 2, s)), (TransferFunction(5, s*(s**2 + 2), s), TransferFunction(10, s, s))))
>>> pprint(H_1, use_unicode=False)  # pprint() for better visualization on terminal
[    5        5*s  ]   
[    -       ------]   
[    s        2    ]   
[            s  + 2]   
[                  ]   
[    5         10  ]   
[----------    --  ]   
[  / 2    \    s   ]   
[s*\s  + 2/        ]{t}
>>> # Default printer is latex for IPython notebooks
>>> H_1.var
s
>>> print(f"Inputs = {H_1.num_inputs} | Outputs = {H_1.num_outputs}")
Inputs = 2 | Outputs = 2
>>> H_1.shape
(2, 2)
```
  There are many other useful methods and attributes of `TransferFunctionMatrix` class like - `transpose()`, `doit()`, index notation (`a[b, c]`) (for accesing individual elements of TFM), `subs()`, `rewrite()`, `expand()`, `simplify()`, `elem_poles()`, `elem_zeros()` etc. Even `evalf()` works with `TransferFunctionMatrix`. Adding examples related to each of these would make this report unnecessarily big so users can try independently. The docs have examples related to each of these methods.

- Just like `TransferFunction`'s `from_rational_expression()`, users can convert `TransferFunctionMatrix` objects to `ImmutableMatrix` objects using `from_Matrix()` (classmethod).
```py
>>> from sympy.abc import s
>>> from sympy.physics.control.lti import TransferFunctionMatrix
>>> from sympy import Matrix, pprint
>>> M = Matrix([[s, 1/s], [1/(s+1), s]])
>>> M_tf = TransferFunctionMatrix.from_Matrix(M, s)  # args -> (Matrix, var)
>>> pprint(M_tf, use_unicode=False)
[  s    1]
[  -    -]
[  1    s]
[        ]
[  1    s]
[-----  -]
[s + 1  1]{t}
```

<h2>Future Work</h2>

The control module still requires a lot of changes to become a powerful control system toolkit like [MATLAB](https://www.mathworks.com/products/control.html). Some of which include -

- 

<h2>Conclusion</h2>
