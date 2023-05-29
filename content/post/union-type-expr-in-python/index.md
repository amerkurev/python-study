---
title: Union Type Expression
description: Another useful use of the | operator.
slug: union-type-expr-in-python
date: 2023-05-29 00:00:00+0000
categories:
    - Language
tags:
    - annotation
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Starting from Python 3.10, you can use Union type expression in some scenarios.

The built-in functions [isinstance()](https://docs.python.org/3/library/functions.html#isinstance) and [issubclass()](https://docs.python.org/3/library/functions.html#issubclass) take a type or tuple of types as their second argument.
Using tuples requires writing additional parentheses.

Consider the code below:
```python
x = 42

print(isinstance(x, (int, str)))  # output: True

print(issubclass(type(x), (int, str)))  # output: True
```

Let's rewrite this example using Union type expression:
```python
x = 42

print(isinstance(x, int | str))  # output: True

print(issubclass(type(x), int | str))  # output: True
```

This example is equivalent to the previous one, but it looks cleaner.
Instead of `(X, Y)`, we write `X | Y` using the | (bitwise or) operator.

Union type expression is particularly expressive when used in type annotations.
Compare the example below without using Union type expression:
```python
from typing import List, Union, Optional

def f(lst: List[Union[int, str]], param: Optional[int]) -> Union[float, str]:
    return ''

f([1, 'abc'], None)
```

And the same example using Union type expression.
```python
def f(lst: list[int | str], param: int | None) -> float | str:
    return ''

f([1, 'abc'], None)
```

We didn't even need to import anything from the **typing** module!

For more information about Union type expression, refer to [PEP 604](https://peps.python.org/pep-0604/).
