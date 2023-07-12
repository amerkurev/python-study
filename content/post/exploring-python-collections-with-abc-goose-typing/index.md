---
title: Exploring Python Collections with ABC and Goose Typing
description: Learn how to use Python's Abstract Base Classes (ABCs) to analyze and categorize standard Python collections based on their properties.
slug: exploring-python-collections-with-abc-goose-typing
date: 2023-07-12 00:00:00+0000
image: cover.jpeg
categories:
    - Language
    - Standard Library
    - Packages
tags:
    - abc
    - rich
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Python's **Abstract Base Classes** (ABCs) in the `collections.abc` module are a set of tools that help us check if a class follows a specific interface.
This is done using the `issubclass()` or `isinstance()` functions.
The interesting part is that the class we're checking doesn't need to inherit from the abstract base type.
It just needs to provide the necessary methods to match the abstract type.

Here's an example:
```python
from collections.abc import Iterable

class MyClass:
    def __iter__(self): ...
    def __next__(self): ...

    
issubclass(MyClass, Iterable)
# Returns True, even though MyClass doesn't inherit from Iterable!

isinstance(MyClass(), Iterable)
# Also True, MyClass doesn't inherit from Iterable, 
# but provides the necessary methods.
```

ABCs are especially useful in a dynamically-typed language like Python, where you often need to inspect an object to determine what it is and what it can do.

Now, let's use ABCs to explore the properties of standard Python collections.
We'll write a program that builds a table showing which collections support which operations.
We'll use the popular package [rich](https://github.com/Textualize/rich) to build the table.
This package is great for formatted output in the terminal.

We'll categorize collections into three types: sequences, mappings, and sets:
- Sequences (like `lists` and `tuples`) support indexing and slicing, so we'll denote them as `x[0:]`.
- Mappings (like `dict`) associate values with keys, so we'll denote them as `x["key"]`.
- Sets support operations like intersection and difference, so we'll denote them as `x & y`.

We'll also check whether each collection is mutable and whether it's hashable (i.e., can be used as a dictionary key).

All this will be determined by a small function written using `collections.abc`.
Here's the code:
```python
from collections import abc

def goose_typing(c: abc.Collection) -> list:
    # The cells of the table will be marked with 'x' if the test passed
    _ = lambda x: 'x' if x is True else ''

    mutable = (abc.MutableSequence, abc.MutableMapping, abc.MutableSet)
    r = [
        c.__name__,
        _(issubclass(c, abc.Sequence)),
        _(issubclass(c, abc.Mapping)),
        _(issubclass(c, abc.Set)),
        _(issubclass(c, mutable)),
        _(issubclass(c, abc.Hashable)),
    ]
    return r
```

The function name, `goose_typing`, is not a mistake.
This approach is indeed called Goose Typing, not Duck Typing.

Now we just need to build the table using the rich package.
Here's the code:
```python
from rich.console import Console
from rich.table import Table

table = Table(title='Python Collections')
table.add_column('', justify='right', style='cyan', no_wrap=True)
table.add_column('x[0:]', justify='center')
table.add_column('x["key"]', justify='center')
table.add_column('x & y', justify='center')
table.add_column('mutable', justify='center')
table.add_column('hashable', justify='center')

from array import array  # We'll also investigate the array class

for c in (array, list, tuple, dict, set, frozenset):
    table.add_row(*goose_typing(c))

console = Console()
console.print(table)
```

Running this script will give you a beautiful table showing the properties of standard Python collections.
```console
                     Python Collections
┏━━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━┓
┃           ┃ x[0:] ┃ x["key"] ┃ x & y ┃ mutable ┃ hashable ┃
┡━━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━┩
│     array │   x   │          │       │    x    │          │
│      list │   x   │          │       │    x    │          │
│     tuple │   x   │          │       │         │    x     │
│      dict │       │    x     │       │    x    │          │
│       set │       │          │   x   │    x    │          │
│ frozenset │       │          │   x   │         │    x     │
└───────────┴───────┴──────────┴───────┴─────────┴──────────┘
```

Using Goose Typing, you can check whether an unknown object you've received matches a certain interface, and then decide how to work with it.

Let's try to investigate something more interesting than `list` or `dict`. For example, this:
```python
# ... right after the loop
d = dict()
table.add_row(*goose_typing(d.keys().__class__))  # dict_keys
```

The result:
```console
                     Python Collections
┏━━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━━┳━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━┓
┃           ┃ x[0:] ┃ x["key"] ┃ x & y ┃ mutable ┃ hashable ┃
┡━━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━━╇━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━┩
│     array │   x   │          │       │    x    │          │
│      list │   x   │          │       │    x    │          │
│     tuple │   x   │          │       │         │    x     │
│      dict │       │    x     │       │    x    │          │
│       set │       │          │   x   │    x    │          │
│ frozenset │       │          │   x   │         │    x     │
│ dict_keys │       │          │   x   │         │          │
└───────────┴───────┴──────────┴───────┴─────────┴──────────┘
```

The result is interesting. The `dict_keys` object, returned by calling `keys()` on a dictionary, behaves like a `set`, but is neither mutable nor hashable.
This means you can find common keys between two dictionaries like this:

```python
d1 = {'1': 1, '2': 2}
d2 = {'1': 1, '3': 3}

print(d1.keys() & d2.keys())  # sets intersection
# Outputs the common keys between two dictionaries: {'1'}
```

ABCs and Goose Typing can help you to write more type-safe code in Python.

You can read about Goose Typing in the wonderful book [Fluent Python, by Luciano Ramalho](https://www.oreilly.com/library/view/fluent-python-2nd/9781492056348/ch13.html).
For more information on ABCs, check out the official [Python documentation](https://docs.python.org/3/library/collections.abc.html).

For more on the rich package, see the [docs](https://rich.readthedocs.io/en/latest/index.html).
