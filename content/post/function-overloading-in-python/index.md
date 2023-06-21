---
title: Function Overloading in Python
description: "No function overloading in Python? No problem! Achieving overloading's effect with the singledispatch decorator."
slug: function-overloading-in-python
date: 2023-05-24 00:00:00+0000
image: cover.jpeg
categories:
    - Standard Library
tags:
    - decorator
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Function overloading is a mechanism in C++ that allows defining functions with the same name but different parameter sets. 
The compiler chooses the appropriate function based on the parameters. Each function can be adapted to work with specific parameter types. 

Here's an example of declaring overloaded functions in C++:

```cpp
// Prototype three write functions.
int write(std::string s);  // Write a string.
int write(double d);  // Write a double.
int write(double d, int p);  // Write a double with a given precision.
```

However, Python does not have a function overloading mechanism, even if we use type annotations.
The following code is incorrect:

```python
def func(arg: float):
    pass

# Warning: this function will simply hide the previous one, as they have the same name!
def func(arg: list):
    pass
```

But using the [functools.singledispatch](https://docs.python.org/3/library/functools.html#functools.singledispatch) decorator, we can achieve behavior similar to function overloading. 
In other words, we can write specialized versions of a function that perform the same logical operation differently depending on the type of the first argument.
When the implementation is chosen based on the type of a single argument, this is known as **single dispatch**.

Here's an example of how it looks in code:

```python
from functools import singledispatch

@singledispatch
def write(arg, other_arg=True):
    print(f'generic version: {arg!r}')


@write.register
def _(arg: float):
    print(f'specialization for float: {arg:.3}')


@write.register
def _(arg: list):
    print(f'specialization for list: {arg!r}')


write(355 / 113)  # output: specialization for float: 3.14

write(['a', 'b'])  # output: specialization for list: ['a', 'b']

write(True)  # output: generic version: True
```

You can also add overloaded implementations to the function by passing the type directly to the `register()` decorator. 
```python
# type annotations are replaced with explicit type passing
@write.register(bool)
def _(arg):
    print(f'specialization for bool: {arg!r}')

write(True)  # output: specialization for bool: True
```

If there is no registered implementation for a specific type, its method resolution order is used to find a more generic implementation. 
The original function decorated with singledispatch is registered for the base object type, which means it is used if no better implementation is found.

Without **singledispatch**, you would need to write `if-else` blocks inside the function to handle different types of arguments.
This approach is brittle and closed to extension. It is difficult to maintain and extend because you need to modify the function every time you want to add support for a new type of argument. 
In contrast, singledispatch allows you to write specialized implementations without changing the original code.

The singledispatch decorator exists starting from Python 3.4, but supports type annotations only starting from Python 3.7. 
For more information, see [PEP 443](https://peps.python.org/pep-0443/) (Single-dispatch generic functions) and [official docs](https://docs.python.org/3/library/functools.html#functools.singledispatch).
