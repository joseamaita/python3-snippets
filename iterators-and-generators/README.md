# Iterators and Generators

## Introduction

Iteration is one of Python's strongest features. At a high level, you 
might simply view iteration as a way to process items in a sequence. 
However, there is so much more that is possible, such as creating your 
own iterator objects, applying useful iteration patterns in 
the `itertools` module, making generator functions, and so forth. This 
section aims to address common problems involving iteration.

### Basic Form of Iteration

**Problem**

You just want to simply loop over all the members of a sequence such as 
a string, list, or tuple.

**Solution**

Use the most common looping construct, which is the `for` statement. 
This allow us to iterate over a collection of items. 


```python
>>> for n in [1, 2, 3, 4, 5, 6, 7, 8, 9]:
...     print("2 to the {0:d} power is {1:d}".format(n, 2**n))
... 
2 to the 1 power is 2
2 to the 2 power is 4
2 to the 3 power is 8
2 to the 4 power is 16
2 to the 5 power is 32
2 to the 6 power is 64
2 to the 7 power is 128
2 to the 8 power is 256
2 to the 9 power is 512
```

In this example, the variable `n` will be assigned successive items from 
the list `[1, 2, 3, 4, ..., 9]` on each iteration. Because looping over 
ranges of integers is quite common, the following shortcut is often used 
for that purpose:

```
>>> for n in range(1, 10):
...     print("2 to the {0:d} power is {1:d}".format(n, 2**n))
... 
2 to the 1 power is 2
2 to the 2 power is 4
2 to the 3 power is 8
2 to the 4 power is 16
2 to the 5 power is 32
2 to the 6 power is 64
2 to the 7 power is 128
2 to the 8 power is 256
2 to the 9 power is 512
```

The `range(i,j [,stride])` function creates an object that represents a 
range of integers with values *i* to *j-1*. If the starting value is 
omitted, it's taken to be zero. An optional stride can also be given as 
a third argument. Let's see:

```python
>>> a = range(5)
>>> a
range(0, 5)
>>> list(a)
[0, 1, 2, 3, 4]
>>> b = range(1, 8)
>>> b
range(1, 8)
>>> list(b)
[1, 2, 3, 4, 5, 6, 7]
>>> c = range(0, 14, 3)
>>> c
range(0, 14, 3)
>>> list(c)
[0, 3, 6, 9, 12]
>>> d = range(8, 1, -1)
>>> d
range(8, 1, -1)
>>> list(d)
[8, 7, 6, 5, 4, 3, 2]
```

The `for` loop is one of Python's most powerful language features 
because you can create custom iterator objects and generator functions 
that supply it with sequences of values.

### Basic Generator

**Problem**

Instead of returning a single value, you want a basic function that can 
generate an entire sequence of results.

**Solution**

Use the generation construct, which is the `yield` statement. This allow 
us to generate an entire sequence of results.

```python
>>> def countdown(n):
...     print("Counting down!")
...     while n > 0:
...         yield n    # Generate a value (n)
...         n -= 1
...
```

Any function that uses `yield` is known as a *generator*. Calling a 
generator function creates an object that produces a sequence of results 
through successive calls to a `__next__()` method. Let's see:

```python
>>> cu = countdown(3)
>>> cu.__next__()
Counting down!
3
>>> cu.__next__()
2
>>> cu.__next__()
1
```

The `__next()__` call makes a generator function run until it reaches 
the next `yield` statement. At this point, the value passed to `yield` 
is returned by `__next__()`, and the function suspends execution. The 
function resumes execution on the statement following `yield` 
when `__next__()` is called again. This process continues until the 
function returns.

Normally you would not manually call `__next__()` as shown. Instead, you 
would use a `for` loop like this:

```python
>>> for i in countdown(8):
...     print(i, end=' ')
... 
Counting down!
8 7 6 5 4 3 2 1
```

Generators are an extremely powerful way of writing programs based on 
processing pipelines, streams, or data flow.

### Generator For Monitoring Log Files

**Problem**

You want to apply a command tool similar to UNIX `tail -f`.

**Solution**

Use the following generator function that mimics the behavior of the 
UNIX `tail -f` command, which is commonly used to monitor log files:

```python
# tail a file (like tail -f)

import time

def tail(f):
    f.seek(0,2)    # Move to EOF
    while True:
        line = f.readline()    # Try reading a new line of text
        if not line:    # If nothing, sleep briefly and try again
            time.sleep(0.1)
            continue
        yield line
```

### Generator That Looks for A Specific Substring

**Problem**

You want to look for a specific substring.

**Solution**

Here's a generator that looks for a specific substring in a sequence of 
lines:

```python
def grep(lines, searchtext):
    for line in lines:
        if searchtext in line: yield line
```

A subtle aspect of generators is that they are often mixed together with 
other iterable objects such as lists or files. Specifically, when you 
write a statement such as `for item in s`, `s` could represent a list of 
items, the lines of a file, the result of a generator function, or any 
number of other objects that support iteration. The fact that you can 
just plug different objects in for `s` can be a powerful tool for 
creating extensible programs.
