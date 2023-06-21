---
title: Dataclasses in Python
description: Learn about the different ways to create data classes in Python and choose the best approach for your needs.
slug: dataclasses-in-python
date: 2023-06-21 00:00:00+0000
categories:
    - Standard Library
    - Packages
tags:
    - namedtuple
    - dataclass
    - attrs
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Dataclasses are simple classes that generally only have a set of fields, without additional functionality (methods).
Dataclasses are similar to structures in the C language - a composite data type that encapsulates a set of values of different types without hiding them.

On the one hand, a data class can be represented in Python as an ordinary class.
But this requires the programmer to perform routine actions: defining the `__init__`, `__str__`, comparison methods `__eq__`, etc.

In Python, you can create a data class much easier. Let's consider 4 approaches to this: from simple to most powerful.


### Approach 1. Named tuples collections.namedtuple
```python
import datetime
from collections import namedtuple

field_names = 'title author isbn pub_date rating'
Book = namedtuple('Book', field_names, defaults=[4.98])

# Book is a subclass of tuple
print(issubclass(Book, tuple))  # output: True

book = Book(
    title='Learning Python',
    author='Mark Lutz',
    isbn=1449355730,
    pub_date=datetime.date(2013, 7, 30),
)

print(book.title)  # Learning Python
print(book.rating)  # 4.98, by default
print(type(book))  # <class '__main__.Book'>
print(book)  # Book(title='Learning Python', author='Mark...)
```

**collections.namedtuple** is a factory that creates subclasses of tuple, as indicated by `issubclass`.
We can access data fields by name (`book.title`), and this will be the same for all data classes in the future.
In addition to the useful `__str__` method, which outputs a good representation of the object, we also get a meaningful `__eq__` method.


### Approach 2. Typed named tuples typing.NamedTuple

```python
import datetime
from typing import NamedTuple


class Book(NamedTuple):
    title: str
    author: str
    isbn: int
    pub_date: datetime.datetime
    rating: float = 4.98


# Book is a subclass of tuple
print(issubclass(Book, tuple))  # output: True

book = Book(
    title='Learning Python',
    author='Mark Lutz',
    isbn=1449355730,
    pub_date=datetime.date(2013, 7, 30),
)

print(book.title)  # Learning Python
print(book.rating)  # 4.98, by default
print(type(book))  # <class '__main__.Book'>
print(book)  # Book(title='Learning Python', author='Mark...)
```

As can be seen from the example, the use of the data class Book has not changed at all.
**typing.NamedTuple** offers the same functionality and additionally type annotations for each field.
The class syntax can be used with **typing.NamedTuple** starting from Python 3.6.
This allows you to add your own methods or override existing ones, for example, define your own `__str__ `method.
Book is still a subclass of tuple.


### Approach 3. Decorator @dataclass

```python
import datetime
from dataclasses import dataclass


@dataclass(frozen=True)
class Book:
    title: str
    author: str
    isbn: int
    pub_date: datetime.datetime
    rating: float = 4.98


# Book is not a subclass of tuple
print(issubclass(Book, tuple))  # output: False

book = Book(
    title='Learning Python',
    author='Mark Lutz',
    isbn=1449355730,
    pub_date=datetime.date(2013, 7, 30),
)

print(book.title)  # Learning Python
print(book.rating)  # 4.98, by default
print(type(book))  # <class '__main__.Book'>
print(book)  # Book(title='Learning Python', author='Mark...)
```

Again, the use of the data class Book has not changed. The class syntax is used, and you can define your own methods.
But there is a significant difference from the previous examples: the **@dataclass** decorator does not rely on inheritance from tuple.
And this is rather good! In addition, the **@dataclass** decorator takes several parameters, such as `frozen`, which configure the operation of the data class.
You can read about them in the [documentation](https://docs.python.org/3/library/dataclasses.html#dataclasses.dataclass).
The **@dataclass** decorator can be used starting from Python 3.7. The dataclasses module was first described in [PEP 557](https://peps.python.org/pep-0557/).


### Approach 4. External package _attrs_

```python
import datetime
from attrs import define


@define
class Book:
    title: str
    author: str
    isbn: int
    pub_date: datetime.datetime
    rating: float = 4.98


# Book is not a subclass of tuple
print(issubclass(Book, tuple))  # output: False

book = Book(
    title='Learning Python',
    author='Mark Lutz',
    isbn=1449355730,
    pub_date=datetime.date(2013, 7, 30),
)

print(book.title)  # Learning Python
print(book.rating)  # 4.98, by default
print(type(book))  # <class '__main__.Book'>
print(book)  # Book(title='Learning Python', author='Mark...)
```

The [**attrs**](https://www.attrs.org/en/stable/) package is external and is not included in the standard Python distribution.
It must be [installed](https://pypi.org/project/attrs/) separately. At first glance, there are no differences from **@dataclass** at all.
Then why use an external package? Here's how the **attrs** package developers answer [this question](https://www.attrs.org/en/stable/why.html#why-not).

Which approach to choose depends on the situation and preferences.
Perhaps named tuples will be more than enough for you. But knowing about each of the approaches is useful in any case.
