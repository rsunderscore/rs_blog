# A walk-through of itertools
In gereral iterables are objects with multiple elements that you can step through in a for loop, things like: list, tuples, and sets.  Ieterables can also be any class that defines 
`__iter__` and `__next__` dunder methods. Generators are also iterators but their contents are not defined until requested, 
and could potentially lead to infinite loops. Many of the itertools functions return generators 
(e.g. cycle, count, repeat). Strings can also be treated as iterables under certain conditions. 
Itertools also has tools to create an iterable from something that is otherwise not one. 
## Itertools
https://docs.python.org/3/library/itertools.html

`pairwise` (python 3.10+) This is a recent addition so if you are not on the latest python it won't yet be available to you, in the meantime you can use pairwise from more-itertools (see below).
The documentation includes recipes that explain how to perform various additional iterable manipulations with the tools provided. Many of these are implemented as functions in more-itertools and a documented as itertools recipes: pairwise, partition, convolve, etc.

## More-itertools
https://more-itertools.readthedocs.io/en/stable/index.html

more-itertools is not part of the standard library and requires installation

`pip install more-itertools`

or the conda equivalent using the conda-forge channel

`conda install -c conda-forge more-itertools`

```py
print('testing code block')
```
