---
title: "Python Variable Scopes: Understanding Local, Global, and Nonlocal"
description: A simple description of all variable scopes in Python.
slug: variable-scopes-local-global-nonlocal
date: 2023-05-21 00:00:00+0000
categories:
    - Language
tags:
    - nonlocal
    - global
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

In Python, there are three variable scopes: global, local, and nonlocal. 

### Local scope 
Local scope refers to variables inside a function that can only be accessed within that function. 
Control blocks like `if`, `while`, and `for` do not create a new local scope. Variables inside them belong to the enclosing function.

```python
def local_scope():
    if True:
        x = 'x'
    print(f'{x} is local')  # outputs `x is local`

print(f'{x} is local')  # raises NameError: name 'x' is not defined
```

### Global scope
Global scope refers to variables declared outside functions that can be accessed throughout the program. 
Technically, Python does not have program-level scope, only module-level scope. 
Global variables can be accessed from local scope, but to modify them from local scope, you need to use the `global` keyword.

The following example shows that Python does not consider `X` a global variable because of the assignment inside the function without the `global` keyword. 
The error occurs when `print` is called, even before the actual assignment.

```python
X = 'X'

def global_scope_wrong():
    print(f'{X} is global')
    X += 'Y'

global_scope_wrong()  # raises UnboundLocalError: local variable 'X' referenced before assignment
```

The correct way to assign a global variable inside a function is to use the `global` keyword.

```python
X = 'X'

def global_scope():
    global X
    print(f'{X} is global')
    X += 'Y'

global_scope()  # outputs 'X is global'

global_scope()  # outputs 'XY is global'
```

### Nonlocal scope
Nonlocal scope is a special scope that only exists for nested functions. 
Nested functions are used, for example, to create [decorators](https://python.study/p/parameterized-decorators/). 
Variables declared in the function that creates the decorator will be available inside the created decorator function, even after the local scope of the enclosing function is destroyed. 
This is called a **closure**.

```python
def outer_func():
    # closure
    x = 'x'  # local for outer_func, nonlocal for inner_func

    def inner_func():
        print(f'{x} is free')
    return inner_func

f = outer_func()
f()  # outputs 'x is free'
```

To modify a free variable from nonlocal scope, you need to use the `nonlocal` keyword, similar to using `global`.

```python
def outer_func():
    # closure
    x = 'x'  # local for outer_func, nonlocal for inner_func

    def inner_func():
        # raises UnboundLocalError if nonlocal is not added
        nonlocal x
        print(f'{x} is free')
        x += 'y'
    return inner_func

f = outer_func()
f()  # outputs 'x is free'
f()  # outputs 'xy is free'
```

Understanding variable scopes is essential for writing correct Python code.
