---
title: Keyword-Only and Positional-Only Parameters in Function
description: "Don't know what \"def f(a, /, b, *, c)\" signature means in Python? OK, read this short explanation."
slug: keyword-and-positional-only-params-in-func
date: 2023-05-23 00:00:00+0000
categories:
    - Language
tags:
    - function
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

In Python, function parameters can be Positional-Only, Positional-or-Keyword, or Keyword-Only. 
Let's consider all three cases in the example function `f`:

```python
def f(pos1, pos2, /, pos_or_kwd1, pos_or_kwd2=None, *, kwd1, kwd2):
    # pos1, pos2 - are Positional-Only
    # pos_or_kwd1, pos_or_kwd2 - are Positional-or-Keyword
    # kwd1, kwd2 - are Keyword-Only
    pass
```

The rules for defining these parameters are straightforward:

1. Everything before the `/` symbol is Positional-Only parameters.
2. Everything after the `*` symbol is Keyword-Only parameters.
3. What's between `/` and `*` can be passed as both Positional and Keyword.
4. If there is no `/` symbol, there are no Positional-Only parameters.
5. If there is no `*` symbol, there are no Keyword-Only parameters.
6. If `/` and `*` are not present, arguments can be passed to a function by position or by keyword.

Here are some valid calls to this function:

```python
f(1, 2, 'a', kwd1='c', kwd2='d')  # pos_or_kwd1 = 'a', pos_or_kwd2 = None
f(1, 2, 'a', 'b', kwd1='c', kwd2='d')  # pos_or_kwd1 = 'a', pos_or_kwd2 = 'b'
f(1, 2, 'a', pos_or_kwd2='b', kwd1='c', kwd2='d')
f(1, 2, pos_or_kwd1='a', kwd1='c', kwd2='d')
```

Note that the first two arguments are Positional-Only, and we cannot pass them as `pos1=1`. 
Meanwhile, the last two arguments are Keyword-Only, and we cannot omit the use of the name when passing the argument value.

Here are some invalid calls to the function `f` that will raise an exception:

```python
f(1, 2, 'a', 'b', 'c', kwd2='d')  # raises... 
# TypeError: f() takes from 3 to 4 positional arguments but 5 positional arguments (and 1 Keyword-Only argument) were given

f(pos1=1, pos2=2, pos_or_kwd1='a', kwd1='c', kwd2='d')  # raises... 
# TypeError: f() got some Positional-Only arguments passed as keyword arguments: 'pos1, pos2'
```

The logic around optional values for Positional-Only parameters remains the same as for Positional-or-Keyword parameters. For example:

```python
def f(pos1, pos2=2, /, pos_or_kwd1='a', *, kwd1, kwd2):
    pass
```

Once a Positional-Only parameter is specified with a default, 
the following Positional-Only and Positional-or-Keyword parameters need to have defaults as well.

As guidance, use Positional-Only if names do not matter or have no meaning, and there are only a few arguments that will always be passed in the same order. 
Use Keyword-Only when names have meaning, and the function definition is more understandable by being explicit with names.

For more information, refer to [PEP 3102](https://peps.python.org/pep-3102/) (Keyword-Only Arguments) and [PEP 570](https://peps.python.org/pep-0570/) (Python Positional-Only Parameters).
