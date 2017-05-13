# Data Encoding and Processing

## Introduction

The main focus of this section is using Python to process data presented 
in different kinds of common encodings, such as CSV files, JSON, XML, 
and binary packed records. Unlike the part on data structures, this 
section is not focused on specific algorithms, but instead on the 
problem of getting data in and out of a program.

### Reading and Writing CSV Data

**Problem**

You want to read or write data encoded as a CSV file.

**Solution**

For most kinds of CSV data, use the `csv` library. For example, suppose 
you have some stock market data in a file named *stocks.csv* like this:

```csv
Symbol,Price,Date,Time,Change,Volume
"AA",39.48,"6/11/2007","9:36am",-0.18,181800
"AIG",71.38,"6/11/2007","9:36am",-0.15,195500
"AXP",62.58,"6/11/2007","9:36am",-0.46,935000
"BA",98.31,"6/11/2007","9:36am",+0.12,104800
"C",53.08,"6/11/2007","9:36am",-0.25,360900
"CAT",78.29,"6/11/2007","9:36am",-0.23,225400
```

Here's how you would read the data as a sequence of lists:

```python
# stocks_a.py

import csv

# (a) Reading as lists

print('Reading as lists: \n')
with open('stocks.csv') as f:
    f_csv = csv.reader(f)
    headers = next(f_csv)
    print('    ', headers)
    for row in f_csv:
        # process row
        print('    ', row)
```

The output for `$ python3.6 stocks_a.py` is:

```
Reading as lists: 

     ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
     ['AA', '39.48', '6/11/2007', '9:36am', '-0.18', '181800']
     ['AIG', '71.38', '6/11/2007', '9:36am', '-0.15', '195500']
     ['AXP', '62.58', '6/11/2007', '9:36am', '-0.46', '935000']
     ['BA', '98.31', '6/11/2007', '9:36am', '+0.12', '104800']
     ['C', '53.08', '6/11/2007', '9:36am', '-0.25', '360900']
     ['CAT', '78.29', '6/11/2007', '9:36am', '-0.23', '225400']
```

Here's how you would read the data as a sequence of tuples:

```python
# stocks_b.py

import csv

# (b) Reading as tuples

print('Reading as tuples: \n')
with open('stocks.csv') as f:
    f_csv = csv.reader(f)
    headers = next(f_csv)
    print('    ', headers)
    for row_l in f_csv:
        # process row
        row = tuple(row_l)
        print('    ', row)
```

The output for `$ python3.6 stocks_b.py` is:

```
Reading as tuples: 

     ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
     ('AA', '39.48', '6/11/2007', '9:36am', '-0.18', '181800')
     ('AIG', '71.38', '6/11/2007', '9:36am', '-0.15', '195500')
     ('AXP', '62.58', '6/11/2007', '9:36am', '-0.46', '935000')
     ('BA', '98.31', '6/11/2007', '9:36am', '+0.12', '104800')
     ('C', '53.08', '6/11/2007', '9:36am', '-0.25', '360900')
     ('CAT', '78.29', '6/11/2007', '9:36am', '-0.23', '225400')
```

In the preceding code, `row` will be a tuple. Thus, to access certain 
fields, you will need to use indexing, such as `row[0]` (Symbol) 
and `row[4]` (Change).

Since such indexing can often be confusing, this is one place where you 
might want to consider the use of named tuples. For example:

```python
# stocks_c.py

import csv
from collections import namedtuple

# (c) Reading as namedtuples

print('Reading as namedtuples: \n')
with open('stocks.csv') as f:
    f_csv = csv.reader(f)
    Row = namedtuple('Row', next(f_csv))
    for r in f_csv:
        row = Row(*r)
        # process row
        print('    ', row)
        # print('    ', row.Symbol)
```

The output for `$ python3.6 stocks_c.py` is:

```
Reading as namedtuples: 

     Row(Symbol='AA', Price='39.48', Date='6/11/2007', Time='9:36am', Change='-0.18', Volume='181800')
     Row(Symbol='AIG', Price='71.38', Date='6/11/2007', Time='9:36am', Change='-0.15', Volume='195500')
     Row(Symbol='AXP', Price='62.58', Date='6/11/2007', Time='9:36am', Change='-0.46', Volume='935000')
     Row(Symbol='BA', Price='98.31', Date='6/11/2007', Time='9:36am', Change='+0.12', Volume='104800')
     Row(Symbol='C', Price='53.08', Date='6/11/2007', Time='9:36am', Change='-0.25', Volume='360900')
     Row(Symbol='CAT', Price='78.29', Date='6/11/2007', Time='9:36am', Change='-0.23', Volume='225400')
```

This would allow you to use the column headers such as `row.Symbol` 
and `row.Change` instead of indices. It should be noted that this only 
works if the column headers are valid Python identifiers. If not, you 
might have to massage the initial headings (e.g., replacing 
nonidentifier characters with underscores or similar).

Another alternative is to read the data as a sequence of dictionaries 
instead. To do that, use this code:

```python
# stocks_d.py

import csv

# (d) Reading as dictionaries

print('Reading as dictionaries: \n')
with open('stocks.csv') as f:
    f_csv = csv.DictReader(f)
    for row in f_csv:
        # process row
        print('    ', row)
        # print('    ', row['Symbol'])
        # print('    ', row['Change'])
```

In this version, you would access the elements of each row using the row 
headers. For example, `row['Symbol']` or `row['Change']`.

The output for `$ python3.6 stocks_d.py` is:

```
Reading as dictionaries: 

     OrderedDict([('Symbol', 'AA'), ('Price', '39.48'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '-0.18'), ('Volume', '181800')])
     OrderedDict([('Symbol', 'AIG'), ('Price', '71.38'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '-0.15'), ('Volume', '195500')])
     OrderedDict([('Symbol', 'AXP'), ('Price', '62.58'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '-0.46'), ('Volume', '935000')])
     OrderedDict([('Symbol', 'BA'), ('Price', '98.31'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '+0.12'), ('Volume', '104800')])
     OrderedDict([('Symbol', 'C'), ('Price', '53.08'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '-0.25'), ('Volume', '360900')])
     OrderedDict([('Symbol', 'CAT'), ('Price', '78.29'), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', '-0.23'), ('Volume', '225400')])
```

To write CSV data, you also use the `csv` module but create a writer 
object. For example:

```python
# stocks_e.py

import csv

# (e) Writing as list of tuples

print('Writing as list of tuples ... \n')

headers = ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
rows = [
('AA', 39.48, '6/11/2007', '9:36am', -0.18, 181800), 
('AIG', 71.38, '6/11/2007', '9:36am', -0.15, 195500), 
('AXP', 62.58, '6/11/2007', '9:36am', -0.46, 935000), 
]

with open('stocks_lt.csv','w') as f:
    f_csv = csv.writer(f)
    f_csv.writerow(headers)
    f_csv.writerows(rows)
```

The `stocks_lt.csv` file generated from `$ python3.6 stocks_e.py` is:

```
Symbol,Price,Date,Time,Change,Volume
AA,39.48,6/11/2007,9:36am,-0.18,181800
AIG,71.38,6/11/2007,9:36am,-0.15,195500
AXP,62.58,6/11/2007,9:36am,-0.46,935000
```

If you have the data as a sequence of dictionaries, do this:

```python
# stocks_f.py

import csv

# (f) Writing as list of dictionaries

print('Writing as list of dictionaries ... \n')

headers = ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
rows = [
{'Symbol': 'AA', 'Price': 39.48, 'Date': '6/11/2007', 
'Time': '9:36am', 'Change': -0.18, 'Volume': 181800}, 
{'Symbol': 'AIG', 'Price': 71.38, 'Date': '6/11/2007', 
'Time': '9:36am', 'Change': -0.15, 'Volume': 195500}, 
{'Symbol': 'AXP', 'Price': 62.58, 'Date': '6/11/2007', 
'Time': '9:36am', 'Change': -0.46, 'Volume': 935000},
]

with open('stocks_ld.csv','w') as f:
    f_csv = csv.DictWriter(f, headers)
    f_csv.writeheader()
    f_csv.writerows(rows)
```

The `stocks_ld.csv` file generated from `$ python3.6 stocks_f.py` is:

```
Symbol,Price,Date,Time,Change,Volume
AA,39.48,6/11/2007,9:36am,-0.18,181800
AIG,71.38,6/11/2007,9:36am,-0.15,195500
AXP,62.58,6/11/2007,9:36am,-0.46,935000
```

**Discussion**

You should almost always prefer the use of the `csv` module over 
manually trying to split and parse CSV data yourself. For instance, you 
might be inclined to just write some code like this:

```python
# stocks_g.py

# (g) Parsing CSV file yourself without csv module

print('Parsing CSV file yourself without csv module ... \n')

with open('stocks.csv') as f:
    for line in f:
        row = line.split(',')
        # process row
        print('    ', row)
```

The output for `$ python3.6 stocks_g.py` is:

```
Parsing CSV file yourself without csv module ... 

     ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume\n']
     ['"AA"', '39.48', '"6/11/2007"', '"9:36am"', '-0.18', '181800\n']
     ['"AIG"', '71.38', '"6/11/2007"', '"9:36am"', '-0.15', '195500\n']
     ['"AXP"', '62.58', '"6/11/2007"', '"9:36am"', '-0.46', '935000\n']
     ['"BA"', '98.31', '"6/11/2007"', '"9:36am"', '+0.12', '104800\n']
     ['"C"', '53.08', '"6/11/2007"', '"9:36am"', '-0.25', '360900\n']
     ['"CAT"', '78.29', '"6/11/2007"', '"9:36am"', '-0.23', '225400\n']
```

The problem with this approach is that you'll still need to deal with 
some nasty details. For example, if any of the fields are surrounded by 
quotes, you'll have to strip the quotes. In addition, if a quoted field 
happens to contain a comma, the code will break by producing a row with 
the wrong size.

By default, the `csv` library is programmed to understand CSV encoding 
rules used by Microsoft Excel. This is probably the most common variant, 
and will likely give you the best compatibility. However, if you consult 
the documentation for `csv`, you'll see a few ways to tweak the encoding 
to different formats (e.g., changing the separator character, etc.).

For example, let's consider the following tab-delimited file 
called *stocks.tsv*:

```tsv
Symbol	Price	Date	Time	Change	Volume
"AA"	39.48	"6/11/2007"	"9:36am"	-0.18	181800
"AIG"	71.38	"6/11/2007"	"9:36am"	-0.15	195500
"AXP"	62.58	"6/11/2007"	"9:36am"	-0.46	935000
"BA"	98.31	"6/11/2007"	"9:36am"	+0.12	104800
"C"	53.08	"6/11/2007"	"9:36am"	-0.25	360900
"CAT"	78.29	"6/11/2007"	"9:36am"	-0.23	225400
```

If you want to read tab-delimited data instead, use this:

```python
# stocks_h.py

import csv

# (h) Reading tab-separated values

print('Reading tab-separated values ... \n')

with open('stocks.tsv') as f:
    f_tsv = csv.reader(f, delimiter='\t')
    for row in f_tsv:
        # Process row
        print('    ', row)
```

The output for `$ python3.6 stocks_h.py` is:

```
Reading tab-separated values ... 

     ['Symbol', 'Price', 'Date', 'Time', 'Change', 'Volume']
     ['AA', '39.48', '6/11/2007', '9:36am', '-0.18', '181800']
     ['AIG', '71.38', '6/11/2007', '9:36am', '-0.15', '195500']
     ['AXP', '62.58', '6/11/2007', '9:36am', '-0.46', '935000']
     ['BA', '98.31', '6/11/2007', '9:36am', '+0.12', '104800']
     ['C', '53.08', '6/11/2007', '9:36am', '-0.25', '360900']
     ['CAT', '78.29', '6/11/2007', '9:36am', '-0.23', '225400']
```

If you're reading CSV data and converting it into named tuples, you need 
to be a little careful with validating column headers. For example, a 
CSV file named *locations.csv* could have a header line containing 
nonvalid identifier characters like this:

```
Street Address,Num-Premises,Latitude,Longitude
5412 N CLARK,10,41.980262,-87.668452
```

This will actually cause the creation of a `namedtuple` to fail with 
a `ValueError` exception. Try this:

```python
# stocks_i.py

import csv
from collections import namedtuple

# (i) Force the ValueError exception

print('Force the ValueError exception: \n')
with open('locations.csv') as f:
    f_csv = csv.reader(f)
    Row = namedtuple('Row', next(f_csv))
    for r in f_csv:
        row = Row(*r)
        # process row
        print('    ', row)
        # print('    ', row.Symbol)
```

The output for `$ python3.6 stocks_i.py` is:

```
Force the ValueError exception ...

Traceback (most recent call last):
  File "stocks_i.py", line 11, in <module>
    Row = namedtuple('Row', next(f_csv))
  File "/usr/local/lib/python3.6/collections/__init__.py", line 400, in namedtuple
    'identifiers: %r' % name)
ValueError: Type names and field names must be valid identifiers: 'Street Address'
```

To work around this, you might have to scrub the headers first. For 
instance, carrying a regex substitution on nonvalid identifier 
characters like this:

```python
# stocks_j.py

import re
import csv
from collections import namedtuple

# (j) Checking for headers as valid identifiers

print('Checking for headers as valid identifiers ... \n')
with open('locations.csv') as f:
    f_csv = csv.reader(f)
    headers = [ re.sub('[^a-zA-Z_]', '_', h) for h in next(f_csv) ]
    Row = namedtuple('Row', headers)
    for r in f_csv:
        row = Row(*r)
        # process row
        print('    ', row)
        # print('    ', row.Symbol)
```

The output for `$ python3.6 stocks_j.py` is:

```
Checking for headers as valid identifiers ... 

     Row(Street_Address='5412 N CLARK', Num_Premises='10', Latitude='41.980262', Longitude='-87.668452')
```

It's also important to emphasize that `csv` does not try to interpret 
the data or convert it to a type other than a string. If such 
conversions are important, that is something you'll need to do yourself. 
Here is one example of performing extra type conversions on CSV data:

```python
# stocks_k.py

import csv

# (k) Reading into named tuples with type conversion

print('Reading into named tuples with type conversion ... \n')

col_types = [str, float, str, str, float, int]
with open('stocks.csv') as f:
    f_csv = csv.reader(f)
    headers = next(f_csv)
    for row in f_csv:
        # Apply conversions to the row items
        row = tuple(convert(value) for convert, value in zip(col_types, row))
        print('    ', row)
```

The output for `$ python3.6 stocks_k.py` is:

```
Reading into named tuples with type conversion ... 

     ('AA', 39.48, '6/11/2007', '9:36am', -0.18, 181800)
     ('AIG', 71.38, '6/11/2007', '9:36am', -0.15, 195500)
     ('AXP', 62.58, '6/11/2007', '9:36am', -0.46, 935000)
     ('BA', 98.31, '6/11/2007', '9:36am', 0.12, 104800)
     ('C', 53.08, '6/11/2007', '9:36am', -0.25, 360900)
     ('CAT', 78.29, '6/11/2007', '9:36am', -0.23, 225400)
```

Alternatively, here is an example of converting selected fields of 
dictionaries:

```python
# stocks_l.py

import csv

# (l) Converting selected dict fields

print('Reading as dicts with type conversion ... \n')

field_types = [ ('Price', float),
                ('Change', float),
                ('Volume', int) ]

with open('stocks.csv') as f:
    for row in csv.DictReader(f):
        row.update((key, conversion(row[key])) 
                   for key, conversion in field_types)
        print('    ', row)
```

The output for `$ python3.6 stocks_l.py` is:

```
Reading as dicts with type conversion ... 

     OrderedDict([('Symbol', 'AA'), ('Price', 39.48), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', -0.18), ('Volume', 181800)])
     OrderedDict([('Symbol', 'AIG'), ('Price', 71.38), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', -0.15), ('Volume', 195500)])
     OrderedDict([('Symbol', 'AXP'), ('Price', 62.58), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', -0.46), ('Volume', 935000)])
     OrderedDict([('Symbol', 'BA'), ('Price', 98.31), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', 0.12), ('Volume', 104800)])
     OrderedDict([('Symbol', 'C'), ('Price', 53.08), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', -0.25), ('Volume', 360900)])
     OrderedDict([('Symbol', 'CAT'), ('Price', 78.29), ('Date', '6/11/2007'), ('Time', '9:36am'), ('Change', -0.23), ('Volume', 225400)])
```

In general, you'll probably want to be a bit careful with such 
conversions, though. In the real world, it's common for CSV files to 
have missing values, corrupted data, and other issues that would break 
type conversions. So, unless your data is guaranteed to be error free, 
that's something you'll need to consider (you might need to add suitable 
exception handling).

Finally, if your goal in reading CSV data is to perform data analysis 
and statistics, you might want to look at 
the [Pandas package](http://pandas.pydata.org). Pandas includes a 
convenient `pandas.read_csv()` function that will load CSV data into 
a `DataFrame` object. From there, you can generate various summary 
statistics, filter the data, and perform other kinds of high-level 
operations. See in this section recipe "Summarizing Data and Performing 
Statistics" for more information.
