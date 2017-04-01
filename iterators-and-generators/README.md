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

### Origin of An Iterator

**Problem**

You want to know how an iterator is defined.

**Solution**

An *iterable* is an object capable of returning its members one at a 
time. An iterable can be used to feed a `for` loop. The built-in 
function `iter()` takes an iterable object and returns an iterator. A 
call to the `next()` function on the iterator gives us the next element 
if any, and raises a `StopIteration` exception otherwise:

```python
>>> z = iter("Marianne")
>>> z
<str_iterator object at 0x7f2575b42358>
>>> next(z)
'M'
>>> next(z)
'a'
>>> next(z)
'r'
>>> next(z)
'i'
>>> next(z)
'a'
>>> next(z)
'n'
>>> next(z)
'n'
>>> next(z)
'e'
>>> next(z)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

### Definition of An Iterator With A Class

**Problem**

You want to make your own definition of an iterator using a class.

**Solution**

*Iterator* objects are required to support two methods, the `__iter__` 
method that returns the iterator object itself, used in `for` and `in` 
statements, and the `__next__` method that returns the next value from 
the iterator. If there is no more items to return, then the `__next__` 
method should raise a `StopIteration` exception. Let's see the class 
definition:

```python
# rangei.py

class range_5:
    def __init__(self):
        self.i = 0
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.i < 5:
            i = self.i
            self.i += 1
            return i
        else:
            raise StopIteration()
```

Test the class with the interpreter like this:

```python
>>> from rangei import range_5
>>> y = range_5()
>>> y
<rangei.range_5 object at 0x7fa42334c358>
>>> next(y)
0
>>> next(y)
1
>>> next(y)
2
>>> next(y)
3
>>> next(y)
4
>>> next(y)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/jose-alberto/python/exercises/iterators/rangei.py", line 14, in __next__
    raise StopIteration()
StopIteration
```

### Manually Consuming an Iterator

**Problem**

You need to process items in an iterable, but for whatever reason, you 
can't or don't want to use a `for` loop.

**Solution**

To manually consume an iterable, use the `next()` function and write 
your code to catch the `StopIteration` exception. For example, this 
example manually reads lines from a file:

```python
with open('/etc/passwd') as f:
    try:
        while True:
            line = next(f)
            print(line, end='')
    except StopIteration:
        pass
```

Normally, `StopIteration` is used to signal the end of iteration. 
However, if you're using `next()` manually (as shown), you can also 
instruct it to return a terminating value, such as `None`, instead. For 
example:

```python
with open('/etc/passwd') as f:
    while True:
        line = next(f, None)
        if line is None:
            break
            print(line, end='')
```

**Discussion**

In most cases, the `for` statement is used to consume an iterable. 
However, every now and then, a problem calls for more precise control 
over the underlying iteration mechanism. Thus, it is useful to know what 
actually happens.

The following interactive example illustrates the basic mechanics of 
what happens during iteration:

```python
>>> l = [1, 2, 3, 4, 5]
>>> # Get the iterator
... 
>>> li = iter(l)    # Invokes l.__iter__()
>>> li
<list_iterator object at 0x7fd5c136d2e8>
>>> # Run the iterator
... 
>>> next(li)        # Invokes li.__next__()
1
>>> next(li)
2
>>> next(li)
3
>>> next(li)
4
>>> next(li)
5
>>> next(li)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

### Delegating Iteration

**Problem**

You have built a custom container object that internally holds a list, 
tuple, or some other iterable. You would like to make iteration work 
with your new container.

**Solution**

Typically, all you need to do is define an `__iter__()` method that 
delegates iteration to the internally held container. For example:

```python
# node.py

class Node:
    def __init__(self, value):
        self._value = value
        self._children = []
    
    def __repr__(self):
        return 'Node({!r})'.format(self._value)
    
    def add_child(self, node):
        self._children.append(node)
    
    def __iter__(self):
        return iter(self._children)
```

The code for the main file is:

```python
# node-main.py

from node import Node

if __name__ == '__main__':
    root = Node(0)
    child1 = Node(1)
    child2 = Node(2)
    root.add_child(child1)
    root.add_child(child2)
    for ch in root:
        print(ch)    # Outputs Node(1), Node(2)
```

The result is:

```python
$ python3.6 node-main.py 
Node(1)
Node(2)
```

In this code, the `__iter__()` method simply forwards the iteration 
request to the internally held `_children` attribute.

**Discussion**

Python's iterator protocol requires `__iter__()` to return a special 
iterator object that implements a `__next__()` method to carry out the 
actual iteration. If all you are doing is iterating over the contents of 
another container, you don't really need to worry about the underlying 
details of how it works. All you need to do is to forward the iteration
request along.

The use of the `iter()` function here is a bit of a shortcut that cleans 
up the code. The statement `iter(s)` simply returns the underlying 
iterator by calling `s.__iter__()`, much in the same way that `len(s)` 
invokes `s.__len__()`.





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
