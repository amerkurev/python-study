---
title: Generator Expressions
description: Sometimes creating a list in Python can be harmful, and it's better to use Generator Expressions.
slug: generator-expressions
date: 2023-05-14 00:00:00+0000
categories:
    - Language
tags:
    - generator
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Generator Expressions (**genexp**) in Python allow you to iterate over elements one at a time without creating a full list in memory. 
This is useful when you only need to iterate over the elements and don't need to store them in memory. 
By doing so, you can conserve memory and improve performance.

For example, let's compare these two code snippets:
```python
sum([x*x for x in range(100_000_000)])

sum(x*x for x in range(100_000_000))
```

In the first case, a list is created using list comprehensions [PEP 202](https://peps.python.org/pep-0202). 
The entire list is created in memory, and only then is the sum calculated. 
This can consume a lot of memory.

In the second case, a generator object is created, which yields the elements of the sequence to the sum function one by one. 
The entire list of a billion values is never created, so this case does not consume unnecessary memory.

To improve memory usage and performance, use **genexp** wherever possible and avoid constructing the entire list unnecessarily. 
For more information about Generator Expressions, refer to [PEP 289](https://peps.python.org/pep-0289/).
