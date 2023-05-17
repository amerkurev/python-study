---
title: Parameterized Decorators
description: Class-based parameterized decorators look better than function-based ones because they reduce the number of nested function layers.
slug: parameterized-decorators
date: 2023-05-16 00:00:00+0000
categories:
    - Language
tags:
    - decorator
---

In Python, decorators are a way to modify the behavior of a function without changing its source code. 
They are a powerful tool that can add extra functionality to your code.

```python
def add_two(func):
    def wrapper(*args, **kwargs):
        num = func(*args, **kwargs)
        return num + 2
    return wrapper

@add_two
def get_num():
    return 10

print(get_num())  # The result will be 12
```

The first example shows how you can use a simple decorator to add extra functionality to a function. 
However, sometimes you need more flexibility in your code. This is where parameterized decorators come in.

```python
def add_x(x):
    def decorator(func):
        def wrapper(*args, **kwargs):
            num = func(*args, **kwargs)
            return num + x
        return wrapper
    return decorator

@add_x(5)
def get_num():
    return 10

print(get_num())  # The result will be 15
```

Parameterized decorators allow you to pass in an argument to the decorator, which in turn modifies the behavior of the wrapped function. 
This can be quite hard to understand because they work with several nested layers of functions. 
It can make it difficult to keep track of which function is being called and what arguments are being passed at each stage, 
possibly leading to errors and making the code harder to work through.

To make things easier, you can implement a parameterized decorator using a class. 
This can help you write more intuitive and clear code because there are fewer levels of nested functions.

```python
class add_x:
    def __init__(self, x):
        self.x = x

    def __call__(self, func):
        def wrapper(*args, **kwargs):
            num = func(*args, **kwargs)
            return num + self.x
        return wrapper

@add_x(5)
def get_num():
    return 10

print(get_num())  # The result will be 15
```

If you want to learn more about the correct way to write decorators, 
a good article to read is Graham Dumpleton's "[How You Implemented Your Python Decorator is Wrong](https://github.com/GrahamDumpleton/wrapt/blob/develop/blog/01-how-you-implemented-your-python-decorator-is-wrong.md)".

> Posted by [amerkurev](https://github.com/amerkurev/)
