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
