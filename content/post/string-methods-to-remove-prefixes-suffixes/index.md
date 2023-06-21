---
title: String methods to remove prefixes and suffixes
description: "New string methods in Python 3.9 that you'll definitely find useful."
slug: string-methods-to-remove-prefixes-suffixes
date: 2023-05-31 00:00:00+0000
image: cover.jpeg
categories:
    - Language
tags:
    - str
links:
  - title: Posted by amerkurev
    description: https://github.com/amerkurev
    website: https://github.com/amerkurev
    image: https://avatars.githubusercontent.com/u/28217522?v=4
---

Python 3.9 introduced two new string methods, `removeprefix()` and `removesuffix()`, for various string objects.
These methods remove a prefix or suffix (respectively) from a string, if present.
The reason for adding these two new methods is that the existing methods, `str.lstrip` and `str.rstrip`, often confuse users.
These methods remove leading or trailing characters, not substrings. This can lead to unintended removal of useful data.

For example, consider the following code:

```python
s = '__trash__ the most useful content'

s = s.lstrip('__trash__ ')

print(s)  # output: 'e most useful content'
```

In this case, not only was the prefix `__trash__` removed, but also part of the useful data (`th`).
This is because `lstrip` removes all characters in the given set from the beginning of the string.

To avoid this, users had to write their own functions or use regular expressions, which can be difficult to read and may contain subtle mistakes.
The new methods `removeprefix()` and `removesuffix()` do exactly what their names suggest, making them more intuitive and easier to use.

Let's rewrite the previous code snippet using the `removeprefix()` method:

```python
s = '__trash__ the most useful content'

s = s.removeprefix('__trash__ ')

print(s)  # output: 'the most useful content'
```

In this case, only the prefix `__trash__` was removed, leaving the useful data intact.

If the prefix is not found, the original string is returned.
```python
s = '__trash__ the most useful content'

s = s.removeprefix('__trash ')  # the prefix is not found

print(s)  # original string: '__trash__ the most useful content'
```

It's worth noting that these new methods are available not only for `str`, but also for binary `bytes` and `bytearray` objects, and `collections.UserString`.

For more information, refer to the documentation for [str](https://docs.python.org/3/library/stdtypes.html#str.removeprefix) or [PEP 616](https://peps.python.org/pep-0616/).
