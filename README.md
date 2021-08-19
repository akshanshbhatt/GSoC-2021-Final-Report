# GSoC 2021 Report - Improve and Expand Functionalities in Control Module

<h2>About Me</h2>

I am [Akshansh Bhatt](https://www.github.com/akshanshbhatt), a 2nd-year undergraduate student pursuing a dual major in Physics and Electronics Engineering at [BITS Pilani](https://bits-pilani.ac.in/). I was part of the Google Summer of Code 2021 program as a student developer at [SymPy](https://github.com/sympy/sympy). You can find my project [here](https://summerofcode.withgoogle.com/projects/#6135134387961856). For information related to my weekly progress in the program, you can check out my [blog posts](https://gsoc-blog.akshansh.me/). I am available for discussions/feedback on this [email id](mailto:qaz.akshansh@gmail.com).

<h2>Project Synopsis</h2>

My project aimed to achieve 3 main goals -

- Complete the unfinished PR(s) and fix bugs from last year's work by [@namannimmo10](https://www.github.com/namannimmo10).
- Make the control module's API more robust and beginner-friendly by adding examples related to use cases.
- Add functionalities in the control module that were proposed last year, but work didn't start. This mainly included `StateSpace` class and common plots related to control theory.

<h2>Work Accomplished</h2>

#### Major PRs

- [#21634](https://github.com/sympy/sympy/pull/21634) : Improve `TransferFunction` docs and add `to_expr`
- [#21653](https://github.com/sympy/sympy/pull/21653) : [GSoC] Add `TransferFunctionMatrix` class
- [#21703](https://github.com/sympy/sympy/pull/21703) : Implement `MIMOSeries` and `MIMOParallel`
- [#21763](https://github.com/sympy/sympy/pull/21763) : Add graphical analyses in `physics.control`
- [#21833](https://github.com/sympy/sympy/pull/21833) : Implement `MIMOFeedback` class

#### Minor PRs

- [#21587](https://github.com/sympy/sympy/pull/21587) : fix(physics.control): order of classes in doc
- [#21820](https://github.com/sympy/sympy/pull/21820) : Update example in `MIMOParallel.var`

<h2>Work in Progress</h2>

- Adding Textbook examples demonstrating the use cases of the modules functionalities.

<h2>Examples</h2>

In chronological order of addition to the codebase, these are some examples demonstrations of my work -

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
>>> from sympy.abc import s, p, a
>>> from sympy import pprint
>>> from sympy.physics.control.lti import TransferFunction, TransferFunctionMatrix, Series, Parallel
>>> TransferFunction = TF; TransferFunctionMatrix = TFM
>>> tf_1 = TF(s + a, s**2 + s + 1, s)
>>> tf_2 = TF(p**4 - 3*p + 2, s + p, s)
>>> tf_3 = TF(3, s + 2, s)
>>> tf_4 = TF(-a + p, 9*s - 9, s)
>>> tfm_1 = TFM([[tf_1], [tf_2], [tf_3]])
>>> tfm_1
TransferFunctionMatrix(((TransferFunction(a + s, s**2 + s + 1, s),), (TransferFunction(p**4 - 3*p + 2, p + s, s),), (TransferFunction(3, s + 2, s),)))
>>> tfm_1.var
s
>>> tfm_1.num_inputs
1
>>> tfm_1.num_outputs
3
>>> tfm_1.shape
(3, 1)
>>> pprint(tfm_2, use_unicode=False)  # pretty-printing for better visualization
[   a + s           -3       ]
[ ----------       -----     ]
[  2               s + 2     ]
[ s  + s + 1                 ]
[                            ]
[ 4                          ]
[p  - 3*p + 2      -a - s    ]
[------------    ----------  ]
[   p + s         2          ]
[                s  + s + 1  ]
[                            ]
[                 4          ]
[     3        - p  + 3*p - 2]
[   -----      --------------]
[   s + 2          p + s     ]{t}
```

<h2>Future Work</h2>



<h2>Conclusion</h2>
