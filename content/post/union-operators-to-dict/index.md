---
title: Union Operators To dict
description: A new way to merge two dictionaries in Python 3.9.
slug: union-operators-to-dict
date: 2023-05-15 00:00:00+0000
categories:
    - Language
tags:
    - dict
---

Let's take a look at the new operators for dict that appeared in Python 3.9 - merge (|) and update (|=) operators.

The current ways to merge two dicts have several disadvantages:

### dict.update

```d1.update(d2)``` modifies d1 in-place. 

```e = d1.copy(); e.update(d2)``` is not an expression and needs a temporary variable.

### {**d1, **d2}

Dict unpacking looks ugly and is not easily discoverable. 
Few people would be able to guess what it means the first time they see it, or think of it as the "_obvious way_" to merge two dicts.

[As Guido said](https://mail.python.org/archives/list/python-ideas@python.org/message/K4IC74IXE23K4KEL7OUFK3VBC62HGGVF/):

> I’m sorry for PEP 448, but even if you know about ```**d``` in simpler contexts, 
> if you were to ask a typical Python user how to combine two dicts into a new one, 
> I doubt many people would think of ```{**d1, **d2}```. I know I myself had forgotten about it when this thread started!

### d1 | d2

The new operators have the same relationship to the dict.update method as the list concatenate (+) and extend (+=) operators have to list.extend. 
The | operator creates a new dict, while the |= operator updates an existing dict in place.

Key conflicts will be resolved by keeping the rightmost value. It's important to note that dict union is not commutative; in general: ```d | e != e | d```.

To create a new dict, you can use the merge (|) operator. For example:

```python
d1 = {'a': 1, 'b': 3}
d2 = {'a': 2, 'b': 4, 'c': 6}
d3 = d1 | d2  # {'a': 2, 'b': 4, 'c': 6}
```

To update an existing dict in place, you can use the update (|=) operator. For example:

```python
d1 = {'a': 1, 'b': 3}
d2 = {'a': 2, 'b': 4, 'c': 6}
d1 |= d2
d1  # {'a': 2, 'b': 4, 'c': 6}
```

In summary, when you need to merge two dicts, you can use the new merge (|) and update (|=) operators. 
They are more convenient and easier to use than the current ways. 
For more information about Union Operators To dict, refer to [PEP 584](https://peps.python.org/pep-0584/).

> Posted by [amerkurev](https://github.com/amerkurev/)
