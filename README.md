# GSoC 2021 Report: Improve and Expand Functionalities in Control Module (Akshansh Bhatt)

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
- [#19761](https://github.com/sympy/sympy/pull/19761) : [GSoC] Add `TransferFunctionMatrix` class in `physics.control`
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

- Adding and Multiplying `TransferFunctionMatrix` objects return `MIMOSeries` and `MIMOParallel` objects respectively. These systems can be resolved further using `doit()`.
```py
>>> from sympy.abc import s
>>> from sympy.physics.control.lti import TransferFunctionMatrix, TransferFunction
>>> from sympy import pprint
>>> g_11 = TransferFunction(5, s, s)
>>> g_21 = TransferFunction(5, s*(s**2 + 2), s)
>>> G_1 = TransferFunctionMatrix([[g_11], [g_21]])  # TFM
>>> g_12 = TransferFunction(5, s, s)
>>> G_2 = TransferFunctionMatrix([[g_11, g_12]])  # TFM
>>> G = G_1*G_2
>>> G
MIMOSeries(TransferFunctionMatrix(((TransferFunction(5, s, s), TransferFunction(5, s, s)),)), TransferFunctionMatrix(((TransferFunction(5, s, s),), (TransferFunction(5, s*(s**2 + 2), s),))))
>>> pprint(G, use_unicode=False)  # pretty-printed form
[    5     ]             
[    -     ]             
[    s     ]             
[          ]    [5  5]   
[    5     ]   *[-  -]   
[----------]    [s  s]{t}
[  / 2    \]             
[s*\s  + 2/]{t}          
>>> G.doit()  # the equivalent MIMOSeries system
TransferFunctionMatrix(((TransferFunction(25, s**2, s), TransferFunction(25, s**2, s)), (TransferFunction(25, s**2*(s**2 + 2), s), TransferFunction(25, s**2*(s**2 + 2), s))))
>>> pprint(G.doit(), use_unicode=False)  # pretty-printed
[    25           25     ]   
[    --           --     ]   
[     2            2     ]   
[    s            s      ]   
[                        ]   
[     25           25    ]   
[-----------  -----------]   
[ 2 / 2    \   2 / 2    \]   
[s *\s  + 2/  s *\s  + 2/]{t}
>>> h_11 = TransferFunction(5, s, s)
>>> h_12 = TransferFunction(5*s, s**2 + 2, s)
>>> h_21 = TransferFunction(5, s*(s**2 + 2), s)
>>> h_22 = TransferFunction(10, s, s)
>>> H = TransferFunctionMatrix([[h_11, h_12], [h_21, h_22]])  # TFM
>>> eq = G + H
>>> eq  # Addition gives `MIMOParallel` object
MIMOParallel(MIMOSeries(TransferFunctionMatrix(((TransferFunction(5, s, s), TransferFunction(5, s, s)),)), TransferFunctionMatrix(((TransferFunction(5, s, s),), (TransferFunction(5, s*(s**2 + 2), s),)))), TransferFunctionMatrix(((TransferFunction(5, s, s), TransferFunction(5*s, s**2 + 2, s)), (TransferFunction(5, s*(s**2 + 2), s), TransferFunction(10, s, s)))))
>>> pprint(eq, use_unicode=False)  # pretty-printed
                            [    5        5*s  ]   
[    5     ]                [    -       ------]   
[    -     ]                [    s        2    ]   
[    s     ]                [            s  + 2]   
[          ]    [5  5]      [                  ]   
[    5     ]   *[-  -]    + [    5         10  ]   
[----------]    [s  s]{t}   [----------    --  ]   
[  / 2    \]                [  / 2    \    s   ]   
[s*\s  + 2/]{t}             [s*\s  + 2/        ]{t}
>>> eq.doit()  # The equivalent TFM system computed
TransferFunctionMatrix(((TransferFunction(5*(s**2 + 5*s), s**3, s), TransferFunction(5*s**3 + 25*s**2 + 50, s**2*(s**2 + 2), s)), (TransferFunction(5*(s**2*(s**2 + 2) + 5*s*(s**2 + 2)), s**3*(s**2 + 2)**2, s), TransferFunction(5*(2*s**2*(s**2 + 2) + 5*s), s**3*(s**2 + 2), s))))
>>> pprint(_, use_unicode=False)  # pretty-printed
[           / 2      \                 3       2        ]   
[         5*\s  + 5*s/              5*s  + 25*s  + 50   ]   
[         ------------              -----------------   ]   
[               3                       2 / 2    \      ]   
[              s                       s *\s  + 2/      ]   
[                                                       ]   
[  / 2 / 2    \       / 2    \\    /   2 / 2    \      \]   
[5*\s *\s  + 2/ + 5*s*\s  + 2//  5*\2*s *\s  + 2/ + 5*s/]   
[------------------------------  -----------------------]   
[                    2                  3 / 2    \      ]   
[          3 / 2    \                  s *\s  + 2/      ]   
[         s *\s  + 2/                                   ]{t}
```

- Users can also compute closed-loop Feedback for SISO and MIMO systems (both positive and negative).
```py
>>> from sympy.abc import s
>>> from sympy.physics.control.lti import TransferFunction, Feedback, TransferFunctionMatrix, MIMOFeedback
>>> # SISO Feedback Systems
>>> # ---------------------
>>> plant = TransferFunction(3*s**2 + 7*s - 3, s**2 - 4*s + 2, s)
>>> controller = TransferFunction(5*s - 10, s + 7, s)
>>> F1 = Feedback(plant, controller)  # By default negative sign is considered
>>> F1
Feedback(TransferFunction(3*s**2 + 7*s - 3, s**2 - 4*s + 2, s), TransferFunction(5*s - 10, s + 7, s), -1)
>>> pprint(F1, use_unicode=False)  # This is how pretty-printed Feedback object looks like
        /   2          \       
        |3*s  + 7*s - 3|       
        |--------------|       
        |  2           |       
        \ s  - 4*s + 2 /       
-------------------------------
    /   2          \           
1   |3*s  + 7*s - 3| /5*s - 10\
- + |--------------|*|--------|
1   |  2           | \ s + 7  /
    \ s  - 4*s + 2 /           
>>> F2 = Feedback(plant, controller, sign=1)  # Positive Feedback
>>> F2
Feedback(TransferFunction(3*s**2 + 7*s - 3, s**2 - 4*s + 2, s), TransferFunction(5*s - 10, s + 7, s), 1)
>>> pprint(F2, use_unicode=False)
        /   2          \       
        |3*s  + 7*s - 3|       
        |--------------|       
        |  2           |       
        \ s  - 4*s + 2 /       
-------------------------------
    /   2          \           
1   |3*s  + 7*s - 3| /5*s - 10\
- - |--------------|*|--------|
1   |  2           | \ s + 7  /
    \ s  - 4*s + 2 /           
>>> F1.doit()  # doit() gives the resultant TransferFunction
TransferFunction((s + 7)*(s**2 - 4*s + 2)*(3*s**2 + 7*s - 3), ((s + 7)*(s**2 - 4*s + 2) + (5*s - 10)*(3*s**2 + 7*s - 3))*(s**2 - 4*s + 2), s)
>>> F1.doit(cancel=True, expand=True)  # cancel, expand are optional arguments
TransferFunction(3*s**3 + 28*s**2 + 46*s - 21, 16*s**3 + 8*s**2 - 111*s + 44, s)
>>> # MIMO Feedback Systems
>>> # ---------------------
>>> tf1 = TransferFunction(s, 1 - s, s)
>>> tf2 = TransferFunction(1, s, s)
>>> tf3 = TransferFunction(5, 1, s)
>>> tf4 = TransferFunction(s - 1, s, s)
>>> tf5 = TransferFunction(0, 1, s)
>>> sys1 = TransferFunctionMatrix([[tf1, tf2], [tf3, tf4]])
>>> sys2 = TransferFunctionMatrix([[tf3, tf5], [tf5, tf5]])
>>> F_1 = MIMOFeedback(sys1, sys2, 1)  # Positive Feedback
>>> pprint(F_1, use_unicode=False)
/    [  s      1  ]    [5  0]   \-1   [  s      1  ]
|    [-----    -  ]    [-  -]   |     [-----    -  ]
|    [1 - s    s  ]    [1  1]   |     [1 - s    s  ]
|I - [            ]   *[    ]   |   * [            ]
|    [  5    s - 1]    [0  0]   |     [  5    s - 1]
|    [  -    -----]    [-  -]   |     [  -    -----]
\    [  1      s  ]{t} [1  1]{t}/     [  1      s  ]{t}
>>> pprint(F_1.doit(), use_unicode=False)  # Returns the eqivalent MIMO-TF system
[               -s                         1 - s       ]
[             -------                   -----------    ]
[             6*s - 1                   s*(1 - 6*s)    ]
[                                                      ]
[25*s*(s - 1) + 5*(1 - s)*(6*s - 1)  (s - 1)*(6*s + 24)]
[----------------------------------  ------------------]
[        (1 - s)*(6*s - 1)              s*(6*s - 1)    ]{t}
```

- Some basic plots related to control theory. These functions work for any SISO system.
```py
>>> from sympy.abc import s
>>> from sympy.physics.control.lti import TransferFunction
>>> from sympy.physics.control.control_plots import *
>>> tf1 = TransferFunction(s**2 + 1, s**4 + 4*s**3 + 6*s**2 + 5*s + 2, s)
>>> pole_zero_plot(tf1)  # PZ plot
```
![image](https://user-images.githubusercontent.com/53227127/130432074-b08a005c-b555-4b6e-91bd-4068a259d3b8.png)

```py
>>> tf2 = TransferFunction(8*s**2 + 18*s + 32, s**3 + 6*s**2 + 14*s + 24, s)
>>> step_response_plot(tf2)  # Step-Response Plot of a system
```
![image](https://user-images.githubusercontent.com/53227127/130432198-45e82654-6977-4ac6-869b-4cc2bea1da4a.png)

```py
>>> tf3 = TransferFunction(8*s**2 + 18*s + 32, s**3 + 6*s**2 + 14*s + 24, s)
>>> impulse_response_plot(tf3)  # Impulse-Response Plot of a system
```
![image](https://user-images.githubusercontent.com/53227127/130432239-1dba2509-07fb-401a-a87f-2458b56d2666.png)

```py
>>> tf4 = TransferFunction(s, (s+4)*(s+8), s)  # Ramp Response Plot of a system
>>> ramp_response_plot(tf4, upper_limit=2)
```
![image](https://user-images.githubusercontent.com/53227127/130432292-b690d9dd-aab3-4eb8-95d2-baf8c8fa490c.png)

```py
>>> tf5 = TransferFunction(1*s**2 + 0.1*s + 7.5, 1*s**4 + 0.12*s**3 + 9*s**2, s)
>>> bode_plot(tf5, initial_exp=0.2, final_exp=0.7)  # Bode Plot of the system
```
![image](https://user-images.githubusercontent.com/53227127/130432352-8112c55c-2d24-4f3d-a2e3-5039f91e8f75.png)

All these plots are highly customizable and also offer numeric options to the users for analysis or other plotting modules/backends.

<h2>Future Work</h2>

The control module still requires a lot of changes to become a powerful control system toolkit like [MATLAB](https://www.mathworks.com/products/control.html). Some of which include -

- Implementing Discrete-time `TransferFunction` model. Discussing the API and making things compatible with the current implementation without deprecating anything will be a challenging task.
- Introducing `StateSpace` model for effectively representing a State Space system symbolically. I have done some work on it, but it is not satisfactory enough to submit a patch.
- Implementing `root_locus_plot(sys)` and `nyquist_plot(sys)`. I did complete the `root_locus_plot(sys)` during GSoC, but it couldn't get merged due to some performance issue. Those who are interested can refer to the first comment on the plots PR ([#21763](https://github.com/sympy/sympy/pull/21763)) for more details. Also, the `control_plots` module at present only supports SISO systems (`TransferFunction` and its configurations) so, extending the support for MIMO systems can also be a potential future scope. After adding `StateSpace` class, state space models should also be supported by this module.
- Adding support for `TransferFunctionMatrix` objects to be instantiated by passing a list of numerators and a common denominator. This can be an alternative way of object creation.
- Illustrating how a student can solve his textbook problems using this module by adding a few examples.

<h2>Conclusion</h2>

I had an exceptional experience working for SymPy this summer! I want to thank SymPy for giving me a chance to work on such a big project and my mentors - [Naman Gera](https://github.com/namannimmo10) and [Jason Moore](https://github.com/moorepants), for guiding me in every way possible. Apart from my mentors, I would like to acknowledge other people involved in reviewing my code - [Oscar Benjamin](https://github.com/oscarbenjamin), [Ilhan Polat](https://github.com/ilayn), [Eric Wieser](https://github.com/eric-wieser), [Priit Laes](https://github.com/plaes), and [S.Y. Lee](https://github.com/sylee957). This project wouldn't have been achievable without the selfless participation of these people.

I have improved my coding skills by getting exposed to Documentation and Test Driven Development. I got a gist of what it means to write actual 'Pythonic' code, structure any Python project, and find solutions to real-world problems that developers face. Apart from this, I have also developed my communication skills through async communication and weekly video calls with my mentors.

I plan to contribute more to SymPy in the future and help new contributors to make their first step towards open source. I'll be glad to be a mentor in the next edition of GSoC.
