# Numbers, Dates, and Times 

## Introduction

Performing mathematical calculations with integers and floating-point 
numbers is easy in Python. However, if you need to perform calculations 
with fractions, arrays, or dates and times, a bit more work is required. 
The focus of this section is on such topics.

### Rounding Numerical Values

**Problem**

You want to round a floating-point number to a fixed number of decimal 
places.

**Solution**

For simple rounding, use the built-in `round(value, ndigits)` function. 
For example:

```python
>>> round(3.57, 1)
3.6
>>> round(3.54, 1)
3.5
>>> round(-3.57, 1)
-3.6
>>> round(-3.54, 1)
-3.5
>>> round(3.55721, 3)
3.557
```

When a value is exactly halfway between two choices, the behavior of 
round is to round to the nearest even digit. That is, values such as 1.5 
or 2.5 both get rounded to 2.

```python
>>> round(1.5)
2
>>> round(2.5)
2
>>> round(1.5, 0)
2.0
>>> round(2.5, 0)
2.0
```

The number of digits given to `round()` can be negative, in which case 
rounding takes place for tens, hundreds, thousands, and so on. For 
example:

```python
>>> a = 4552256
>>> round(a, -1)
4552260
>>> round(a, -2)
4552300
>>> round(a, -3)
4552000
>>> a = 1627731
>>> round(a, -1)
1627730
>>> round(a, -2)
1627700
>>> round(a, -3)
1628000
```

**Discussion**

Don't confuse rounding with formatting a value for output. If your goal 
is simply to output a numerical value with a certain number of decimal 
places, you don't typically need to use `round()`. Instead, just specify 
the desired precision when formatting. For example:

```python
>>> x = 1.23456
>>> format(x, '0.2f')
'1.23'
>>> format(x, '0.3f')
'1.235'
>>> 'value is {:0.3f}'.format(x)
'value is 1.235'
```

Also, resist the urge to round floating-point numbers to "fix" perceived 
accuracy problems. For example, you might be inclined to do this:

```python
>>> a = 2.1
>>> b = 4.2
>>> c = a + b
>>> c
6.300000000000001
>>> c = round(c, 2)    # "Fix" result (???)
>>> c
6.3
```

For most applications involving floating point, it's simply not 
necessary (or recommended) to do this. Although there are small errors 
introduced into calculations, the behavior of those errors are 
understood and tolerated. If avoiding such errors is important (e.g., in 
financial applications, perhaps), consider the use of the `decimal` 
module, which is discussed in the next recipe.

### Performing Accurate Decimal Calculations

**Problem**

You need to perform accurate calculations with decimal numbers, and 
don't want the small errors that naturally occur with floats.

**Solution**

A well-known issue with floating-point numbers is that they can't 
accurately represent all base-10 decimals. Moreover, even simple 
mathematical calculations introduce small errors. For example:

```python
>>> a = 4.2
>>> b = 2.1
>>> a + b
6.300000000000001
>>> (a + b) == 6.3
False
```

These errors are a "feature" of the underlying CPU and the IEEE 754 
arithmetic performed by its floating-point unit. Since Python's float 
data type stores data using the native representation, there's nothing 
you can do to avoid such errors if you write your code using `float` 
instances.

If you want more accuracy (and are willing to give up some performance), 
you can use the `decimal` module:

```python
>>> from decimal import Decimal
>>> a = Decimal('4.2')
>>> b = Decimal('2.1')
>>> a + b
Decimal('6.3')
>>> print(a + b)
6.3
>>> (a + b) == Decimal('6.3')
True
```

At first glance, it might look a little weird (i.e. specifying numbers 
as strings). However, `Decimal` objects work in every way that you would 
expect them to (supporting all of the usual math operations, etc.). If 
you print them or use them in string formatting functions, they look 
like normal numbers.

A major feature of `decimal` is that it allows you to control different 
aspects of calculations, including number of digits and rounding. To do 
this, you create a local context and change its settings. For example:

```python
>>> from decimal import Decimal
>>> from decimal import localcontext
>>> a = Decimal('1.3')
>>> b = Decimal('1.7')
>>> print(a / b)
0.7647058823529411764705882353
>>> with localcontext() as ctx:
...     ctx.prec = 3
...     print(a / b)
... 
0.765
>>> with localcontext() as ctx:
...     ctx.prec = 50
...     print(a / b)
... 
0.76470588235294117647058823529411764705882352941176
```

**Discussion**

The `decimal` module implements IBM's "General Decimal Arithmetic 
Specification". Needless to say, there are a huge number of 
configuration options that are beyond the recipes presented here.

Newcomers to Python might be inclined to use the `decimal` module to 
work around perceived accuracy problems with the `float` data type. 
However, it's really important to understand your application domain. If 
you're working with science or engineering problems, computer graphics, 
or most things of a scientific nature, it's simply more common to use 
the normal floating-point type. For one, very few things in the real 
world are measured to the 17 digits of accuracy that floats provide. 
Thus, tiny errors introduced in calculations just don't matter. Second, 
the performance of native floats is significantly faster (something 
that's important if you're performing a large number of calculations).

That said, you can't ignore the errors completely. Mathematicians have 
spent a lot of time studying various algorithms, and some handle errors 
better than others. You also have to be a little careful with effects 
due to things such as subtractive cancellation and adding large and 
small numbers together. For example:

```python
>>> nums = [1.23e+18, 1, -1.23e+18]
>>> sum(nums)    # Notice how 1 disappears
0.0
```

This latter example can be addressed by using a more accurate 
implementation in `math.fsum()`:

```python
>>> nums = [1.23e+18, 1, -1.23e+18]
>>> import math
>>> math.fsum(nums)
1.0
```

However, for other algorithms, you really need to study the algorithm 
and understand its error propagation properties.

All of this said, the main use of the `decimal` module is in programs 
involving things such as finance. In such programs, it is extremely 
annoying to have small errors creep into the calculation. 
Thus, `decimal` provides a way to avoid that. It is also common to 
encounter `Decimal` objects when Python interfaces with databases 
(again, especially when accessing financial data).

### Formatting Numbers for Output

**Problem**

You need to format a number for output, controlling the number of 
digits, alignment, inclusion of a thousands separator, and other 
details.

**Solution**

To format a single number for output, use the built-in `format()` 
function. For example:

```python
>>> x = 1234.56789
>>> # Two decimal places of accuracy
...
>>> format(x, '0.2f')
'1234.57'
>>> # Right justified in 10 chars, one-digit accuracy
...
>>> format(x, '>10.1f')
'    1234.6'
>>> # Left justified
...
>>> format(x, '<10.1f')
'1234.6    '
>>> # Centered
... 
>>> format(x, '^10.1f')
'  1234.6  '
>>> # Inclusion of thousands separator
... 
>>> format(x, ',')
'1,234.56789'
>>> format(x, '0,.1f')
'1,234.6'
```

If you want to use exponential notation, change the `f` to an `e` 
or `E`, depending on the case you want used for the exponential 
specifier. For example:

```python
>>> x = 1234.56789
>>> format(x, 'e')
'1.234568e+03'
>>> format(x, '0.2E')
'1.23E+03'
```

The general form of the width and precision in both cases 
is `'[<>^]?width[,]?(.digits)?'` where `width` and `digits` are integers 
and `?` signifies optional parts. The same format codes are also used in 
the `.format()` method of strings. For example:

```python
>>> x = 1234.56789
>>> 'The value is {:0,.2f}'.format(x)
'The value is 1,234.57'
```

**Discussion**

Formatting numbers for output is usually straightforward. The technique 
shown works for both floating-point numbers and `Decimal` numbers in 
the `decimal` module.

When the number of digits is restricted, values are rounded away 
according to the same rules of the `round()` function. For example:

```python
>>> x = 1234.56789
>>> x
1234.56789
>>> format(x, '0.1f')
'1234.6'
>>> format(-x, '0.1f')
'-1234.6'
```

Formatting of values with a thousands separator is not locale aware. If 
you need to take that into account, you might investigate functions in 
the `locale` module. You can also swap separator characters using 
the `translate()` method of strings. For example:

```python
>>> x = 1234.56789
>>> swap_separators = { ord('.'):',', ord(','):'.' }
>>> format(x, ',').translate(swap_separators)
'1.234,56789'
```

In a lot of Python code, numbers are formatted using the `%` operator. 
For example:

```python
>>> x = 1234.56789
>>> '%0.2f' % x
'1234.57'
>>> '%10.1f' % x
'    1234.6'
>>> '%-10.1f' % x
'1234.6    '
```

This formatting is still acceptable, but less powerful than the more 
modern `format()` method. For example, some features (e.g. adding 
thousands separators) aren't supported when using the `%` operator to 
format numbers.

### Working with Binary, Octal, and Hexadecimal Integers

**Problem**

You need to convert or output integers represented by binary, octal, or 
hexadecimal digits.

**Solution**

To convert an integer into a binary, octal, or hexadecimal text string, 
use the `bin()`, `oct()`, or `hex()` functions, respectively:

```python
>>> x = 1234
>>> bin(x)
'0b10011010010'
>>> oct(x)
'0o2322'
>>> hex(x)
'0x4d2'
```

Alternatively, you can use the `format()` function if you don't want 
the `0b`, `0o`, or `0x` prefixes to appear. For example:

```python
>>> x = 1234
>>> format(x, 'b')
'10011010010'
>>> format(x, 'o')
'2322'
>>> format(x, 'x')
'4d2'
```

Integers are signed, so if you are working with negative numbers, the 
output will also include a sign. For example:

```python
>>> x = -1234
>>> format(x, 'b')
'-10011010010'
>>> format(x, 'x')
'-4d2'
```

If you need to produce an unsigned value instead, you'll need to add in 
the maximum value to set the bit length. For example, to show a 32-bit 
value, use the following:

```python
>>> x = -1234
>>> format(2**32 + x, 'b')
'11111111111111111111101100101110'
>>> format(2**32 + x, 'x')
'fffffb2e'
```

To convert integer strings in different bases, simply use the `int()` 
function with an appropriate base. For example:

```python
>>> int('4d2', 16)
1234
>>> int('10011010010', 2)
1234
```

**Discussion**

For the most part, working with binary, octal, and hexadecimal integers 
is straightforward. Just remember that these conversions only pertain to 
the conversion of integers to and from a textual representation. Under 
the covers, there's just one integer type.

Finally, there is one caution for programmers who use octal. The Python 
syntax for specifying octal values is slightly different than many other 
languages. For example, if you try something like this, you'll get a 
syntax error:

```python
>>> import os
>>> os.chmod('script.py', 0755)
  File "<stdin>", line 1
    os.chmod('script.py', 0755)
                             ^
SyntaxError: invalid token
```

Make sure you prefix the octal value with `0o`, as shown here:

```python
>>> import os
>>> os.chmod('script.py', 0o755)
```
