# Data Structures and Algorithms

## Introduction

Python provides a variety of useful built-in data structures, such as 
lists, sets, and dictionaries. For the most part, the use of these 
structures is straightforward. However, common questions concerning 
searching, sorting, ordering, and filtering often arise. Thus, the idea 
of this section is to discuss common data structures and algorithms 
involving data. In addition, treatment is given to the various data 
structures contained in the `collections` module.

### Unpacking a Sequence into Separate Variables

**Problem**

You have an N-element tuple or sequence that you would like to unpack 
into a collection of N variables.

**Solution**

Any sequence (or iterable) can be unpacked into variables using a simple 
assignment operation. The only requirement is that the number of 
variables and structure match the sequence. For example:

```python
>>> tup = (75, "Ricky", 9.81)
>>> x, y, z = tup    # Simple assignment operation
>>> x
75
>>> y
'Ricky'
>>> z
9.81
>>> data = ['SIDR', 125, 32.46, (2017, 2, 6)]
>>> name, shares, price, date = data
>>> name
'SIDR'
>>> shares
125
>>> price
32.46
>>> date
(2017, 2, 6)
>>> name, shares, price, (year, month, day) = data
>>> name
'SIDR'
>>> shares
125
>>> price
32.46
>>> year
2017
>>> month
2
>>> day
6
```

If there is a mismatch in the number of elements, you'll get an error:

```python
>>> x, y = tup
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: too many values to unpack (expected 2)
>>> w, x, y, z = tup
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: not enough values to unpack (expected 4, got 3)
```

**Discussion**

Unpacking actually works with any object that happens to be iterable, 
not just tuples or lists. This includes strings, files, iterators, and 
generators. For example:

```python
>>> name = "Mary"
>>> w, x, y, z = name
>>> w
'M'
>>> x
'a'
>>> y
'r'
>>> z
'y'
```

When unpacking, you may sometimes want to discard certain values. Python 
has no special syntax for this, but you can often just pick a throwaway 
variable name for it. Let's see:

```python
>>> name = "Marianne"
>>> x, y, _, _, _, _, _, z = name
>>> x
'M'
>>> y
'a'
>>> z
'e'
>>> _, shares, price, _ = data
>>> shares
125
>>> price
32.46
```

However, make sure that the variable name you pick isn't being used for 
something else already.
