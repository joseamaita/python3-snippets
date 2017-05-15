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

### Reading and Writing JSON Data

**Problem**

You want to read or write data encoded as JSON (JavaScript Object 
Notation).

**Solution**

The `json` module provides an easy way to encode and decode data in 
JSON. The two main functions are `json.dumps()` and `json.loads()`, 
mirroring the interface used in other serialization libraries, such 
as `pickle`. Here is how you turn a Python data structure into JSON:

```python
>>> import json
>>> data = {
... 'name': 'ACME', 
... 'shares': 100, 
... 'price': 542.23
... }
>>> data
{'name': 'ACME', 'shares': 100, 'price': 542.23}
>>> json_str = json.dumps(data)
>>> json_str
'{"name": "ACME", "shares": 100, "price": 542.23}'
```

Here is how you turn a JSON-encoded string back into a Python data 
structure:

```python
>>> json_str
'{"name": "ACME", "shares": 100, "price": 542.23}'
>>> data_back = json.loads(json_str)
>>> data_back
{'name': 'ACME', 'shares': 100, 'price': 542.23}
```

If you are working with files instead of strings, you can alternatively 
use `json.dump()` and `json.load()` to encode and decode JSON data. For 
example, the data file *data.json* has the following structure:

```
# data.json

{
  "name": "ACME",
  "shares": 100,
  "price": 542.23
}
```

To write JSON data:

```python
# json_a.py

import json

# (a) Writing JSON data

data = {
        'name': 'PDVS', 
        'shares': 500, 
        'price': 1137.87
       }

with open('data_to_write.json', 'w') as f:
    json.dump(data, f)
```

The `data_to_write.json` file generated from `$ python3.6 json_a.py` is:

```
{"name": "PDVS", "shares": 500, "price": 1137.87}
```

To read JSON data:

```python
# json_b.py

import json

# (b) Reading JSON data

print('Reading JSON data ... \n')

with open('data.json', 'r') as f:
    data = json.load(f)
    print('    ', data)
```

The output for `$ python3.6 json_b.py` is:

```
Reading JSON data ... 

     {'name': 'ACME', 'shares': 100, 'price': 542.23}
```

**Discussion**

JSON encoding supports the basic types of `None`, `bool`, `int`
, `float`, and `str`, as well as lists, tuples, and dictionaries 
containing those types. For dictionaries, keys are assumed to be strings 
(any nonstring keys in a dictionary are converted to strings when 
encoding). To be compliant with the JSON specification, you should only 
encode Python lists and dictionaries. Moreover, in web applications, it 
is standard practice for the top-level object to be a dictionary.

The format of JSON encoding is almost identical to Python syntax except 
for a few minor changes. For instance, `True` is mapped to `true`
, `False` is mapped to `false`, and `None` is mapped to `null`. Here is 
an example that shows what the encoding looks like:

```python
>>> import json
>>> json.dumps(True)
'true'
>>> json.dumps(False)
'false'
>>> d = {
... 'a': True,
... 'b': 'Hello',
... 'c': None
... }
>>> d
{'a': True, 'b': 'Hello', 'c': None}
>>> json.dumps(d)
'{"a": true, "b": "Hello", "c": null}'
```

If you are trying to examine data you have decoded from JSON, it can 
often be hard to ascertain its structure simply by printing it out, 
especially if the data contains a deep level of nested structures or a 
lot of fields. To assist with this, consider using the `pprint()` 
function in the `pprint` module. This will alphabetize the keys and 
output a dictionary in a more sane way. Here is an example that 
illustrates how you would pretty print the results of a project search 
from PyPI:

```python
>>> from urllib.request import urlopen
>>> import json
>>> from pprint import pprint
>>> with urlopen('http://pypi.python.org/pypi/Twisted/json') as url:
...     http_info = url.info()
...     raw_data = url.read().decode(http_info.get_content_charset())
... 
>>> project_info = json.loads(raw_data)
>>> pprint(project_info)
{'info': {'_pypi_hidden': False,
          '_pypi_ordering': 56,
          'author': 'Glyph Lefkowitz',
          'author_email': 'glyph@twistedmatrix.com',
          'bugtrack_url': 'https://twistedmatrix.com/trac/',
          'cheesecake_code_kwalitee_id': None,
          'cheesecake_documentation_id': None,
          'cheesecake_installability_id': None,
          'classifiers': ['Programming Language :: Python :: 2.7',
                          'Programming Language :: Python :: 3',
                          'Programming Language :: Python :: 3.3',
                          'Programming Language :: Python :: 3.4',
                          'Programming Language :: Python :: 3.5'],
          'description': 'An extensible framework for Python programming, with '
                         'special focus\n'
                         'on event-based network programming and multiprotocol '
                         'integration.',
          'docs_url': None,
          'download_url': '',
          'downloads': {'last_day': 0, 'last_month': 0, 'last_week': 0},
          'home_page': 'http://twistedmatrix.com/',
          'keywords': '',
          'license': 'MIT',
          'maintainer': '',
          'maintainer_email': '',
          'name': 'Twisted',
          'package_url': 'http://pypi.python.org/pypi/Twisted',
          'platform': '',
          'release_url': 'http://pypi.python.org/pypi/Twisted/17.1.0',
          'requires_python': '',
          'summary': 'An asynchronous networking framework written in Python',
          'version': '17.1.0'},
 'releases': {'1.0.1': [],
              '1.0.3': [],
              '1.0.4': [],
              '1.0.5': [],
              '1.0.6': [],
              '1.0.7': [],
              '1.1.0': [],
              '1.1.1': [],
              '1.2.0': [],
              '10.0.0': [{'comment_text': '',
                          'downloads': 16857,
                          'filename': 'Twisted-10.0.0.tar.bz2',
                          'has_sig': False,
                          'md5_digest': '3b226af1a19b25e3b3e93cc6edf5e284',
                          'packagetype': 'sdist',
                          'path': 'a8/17/f425d2160b92f0c9c69964bece02eab4fc8afb45a7d9a6532c07d81d0610/Twisted-10.0.0.tar.bz2',
                          'python_version': 'source',
                          'size': 2583372,
                          'upload_time': '2010-03-05T22:13:40',
                          'url': 'https://pypi.python.org/packages/a8/17/f425d2160b92f0c9c69964bece02eab4fc8afb45a7d9a6532c07d81d0610/Twisted-10.0.0.tar.bz2'}],
              '2.4.0': [],
              '2.5.0': [],
              '8.0.0': [],
              '8.0.1': [],
              '8.1.0': [],
              '8.2.0': [],
              '9.0.0': [{'comment_text': '',
                         'downloads': 6509,
                         'filename': 'Twisted-9.0.0.tar.bz2',
                         'has_sig': False,
                         'md5_digest': '93fc2756a09ffd1350c046cc940e4311',
                         'packagetype': 'sdist',
                         'path': '3b/b0/c4cafea9ab2e31aa15556eddc151ba987e130cb347b753d991aa2ace768a/Twisted-9.0.0.tar.bz2',
                         'python_version': 'source',
                         'size': 2518268,
                         'upload_time': '2010-03-05T22:21:57',
                         'url': 'https://pypi.python.org/packages/3b/b0/c4cafea9ab2e31aa15556eddc151ba987e130cb347b753d991aa2ace768a/Twisted-9.0.0.tar.bz2'}]},
 'urls': [{'comment_text': '',
           'downloads': 1385,
           'filename': 'Twisted-17.1.0-cp27-cp27m-win_amd64.whl',
           'has_sig': False,
           'md5_digest': '524d0f1483a6a8bf49cd807c9028dea8',
           'packagetype': 'bdist_wheel',
           'path': '84/c4/4bede83bd54cc5a8498b1920b55580da0301efebd0a635d34dc79b3d06dc/Twisted-17.1.0-cp27-cp27m-win_amd64.whl',
           'python_version': 'cp27',
           'size': 3143775,
           'upload_time': '2017-02-11T09:59:53',
           'url': 'https://pypi.python.org/packages/84/c4/4bede83bd54cc5a8498b1920b55580da0301efebd0a635d34dc79b3d06dc/Twisted-17.1.0-cp27-cp27m-win_amd64.whl'},
          {'comment_text': '',
           'downloads': 25658,
           'filename': 'Twisted-17.1.0.tar.bz2',
           'has_sig': False,
           'md5_digest': '5b4b9ea5a480bec9c1449ffb57b2052a',
           'packagetype': 'sdist',
           'path': 'd2/5d/ed5071740be94da625535f4333793d6fd238f9012f0fee189d0c5d00bd74/Twisted-17.1.0.tar.bz2',
           'python_version': 'source',
           'size': 2997334,
           'upload_time': '2017-02-11T09:59:02',
           'url': 'https://pypi.python.org/packages/d2/5d/ed5071740be94da625535f4333793d6fd238f9012f0fee189d0c5d00bd74/Twisted-17.1.0.tar.bz2'}]}
```

The result can be limited to a certain *depth* (ellipsis is used for 
deeper contents):

```python
>>> pprint(project_info, depth=2)
{'info': {'_pypi_hidden': False,
          '_pypi_ordering': 56,
          'author': 'Glyph Lefkowitz',
          'author_email': 'glyph@twistedmatrix.com',
          'bugtrack_url': 'https://twistedmatrix.com/trac/',
          'cheesecake_code_kwalitee_id': None,
          'cheesecake_documentation_id': None,
          'cheesecake_installability_id': None,
          'classifiers': [...],
          'description': 'An extensible framework for Python programming, with '
                         'special focus\n'
                         'on event-based network programming and multiprotocol '
                         'integration.',
          'docs_url': None,
          'download_url': '',
          'downloads': {...},
          'home_page': 'http://twistedmatrix.com/',
          'keywords': '',
          'license': 'MIT',
          'maintainer': '',
          'maintainer_email': '',
          'name': 'Twisted',
          'package_url': 'http://pypi.python.org/pypi/Twisted',
          'platform': '',
          'release_url': 'http://pypi.python.org/pypi/Twisted/17.1.0',
          'requires_python': '',
          'summary': 'An asynchronous networking framework written in Python',
          'version': '17.1.0'},
 'releases': {'1.0.1': [],
              '1.0.3': [],
              '1.0.4': [],
              '1.0.5': [],
              '1.0.6': [],
              '1.0.7': [],
              '1.1.0': [],
              '1.1.1': [],
              '1.2.0': [],
              '10.0.0': [...],
              '10.1.0': [...],
              '10.2.0': [...],
              '11.0.0': [...],
              '11.1.0': [...],
              '12.0.0': [...],
              '12.1.0': [...],
              '12.2.0': [...],
              '12.3.0': [...],
              '13.0.0': [...],
              '13.1.0': [...],
              '13.2.0': [...],
              '14.0.0': [...],
              '14.0.1': [...],
              '14.0.2': [...],
              '15.0.0': [...],
              '15.1.0': [...],
              '15.2.0': [...],
              '15.2.1': [...],
              '15.3.0': [...],
              '15.4.0': [...],
              '15.5.0': [...],
              '16.0.0': [...],
              '16.1.0': [...],
              '16.1.1': [...],
              '16.2.0': [...],
              '16.3.0': [...],
              '16.3.1': [...],
              '16.3.2': [...],
              '16.4.0': [...],
              '16.4.1': [...],
              '16.5.0': [...],
              '16.5.0rc1': [...],
              '16.5.0rc2': [...],
              '16.6.0': [...],
              '16.6.0rc1': [...],
              '16.7.0rc1': [...],
              '16.7.0rc2': [...],
              '17.1.0': [...],
              '17.1.0rc1': [...],
              '2.1.0': [...],
              '2.4.0': [],
              '2.5.0': [],
              '8.0.0': [],
              '8.0.1': [],
              '8.1.0': [],
              '8.2.0': [],
              '9.0.0': [...]},
 'urls': [{...}, {...}]}
```

Normally, JSON decoding will create dicts or lists from the supplied 
data. If you want to create different kinds of objects, supply 
the `object_pairs_hook` or `object_hook` to `json.loads()`. For example, 
here is how you would decode JSON data, preserving its order in 
an `OrderedDict`:

```python
>>> import json
>>> # Some JSON encoded text
...
>>> s = '{"name": "CNTV", "shares": 250, "price": 139.07}'
>>> # Turning JSON into an OrderedDict
...
>>> from collections import OrderedDict
>>> data = json.loads(s, object_pairs_hook=OrderedDict)
>>> data
OrderedDict([('name', 'CNTV'), ('shares', 250), ('price', 139.07)])
```

Here is how you could turn a JSON dictionary into a Python object:

```python
>>> import json
>>> # Some JSON encoded text
...
>>> s = '{"name": "CNTV", "shares": 250, "price": 139.07}'
>>> # Using JSON to populate an instance
...
>>> class JSONObject:
...     def __init__(self, d):
...         self.__dict__ = d
...
>>> data = json.loads(s, object_hook=JSONObject)
>>> data
<__main__.JSONObject object at 0x7fc9dca8a550>
>>> data.name
'CNTV'
>>> data.shares
250
>>> data.price
139.07
```

In this last example, the dictionary created by decoding the JSON data 
is passed as a single argument to `__init__()`. From there, you are free 
to use it as you will, such as using it directly as the instance 
dictionary of the object.

There are a few options that can be useful for encoding JSON. If you 
would like the output to be nicely formatted, you can use the `indent` 
argument to `json.dumps()`. This causes the output to be pretty printed 
in a format similar to that with the `pprint()` function. For example:

```python
>>> import json
>>> data = {
... 'name': 'PDVS',
... 'shares': 500,
... 'price': 1137.87
... }
>>> data
{'name': 'PDVS', 'shares': 500, 'price': 1137.87}
>>> json.dumps(data)
'{"name": "PDVS", "shares": 500, "price": 1137.87}'
>>> print(json.dumps(data))
{"name": "PDVS", "shares": 500, "price": 1137.87}
>>> print(json.dumps(data, indent=4))
{
    "name": "PDVS",
    "shares": 500,
    "price": 1137.87
}
```

If you want the keys to be sorted on output, use the `sort_keys` 
argument:

```python
>>> data
{'name': 'PDVS', 'shares': 500, 'price': 1137.87}
>>> json.dumps(data, sort_keys=True)
'{"name": "PDVS", "price": 1137.87, "shares": 500}'
>>> print(json.dumps(data, sort_keys=True))
{"name": "PDVS", "price": 1137.87, "shares": 500}
>>> print(json.dumps(data, sort_keys=True, indent=4))
{
    "name": "PDVS",
    "price": 1137.87,
    "shares": 500
}
```

Instances are not normally serializable as JSON. For example:

```python
>>> import json
>>> class Point:
...     def __init__(self, x, y):
...         self.x = x
...         self.y = y
... 
>>> p = Point(2, 3)
>>> json.dumps(p)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.6/json/__init__.py", line 231, in dumps
    return _default_encoder.encode(obj)
  File "/usr/local/lib/python3.6/json/encoder.py", line 199, in encode
    chunks = self.iterencode(o, _one_shot=True)
  File "/usr/local/lib/python3.6/json/encoder.py", line 257, in iterencode
    return _iterencode(o, 0)
  File "/usr/local/lib/python3.6/json/encoder.py", line 180, in default
    o.__class__.__name__)
TypeError: Object of type 'Point' is not JSON serializable
```

If you want to serialize instances, you can supply a function that takes 
an instance as input and returns a dictionary that can be serialized. 
For example:

```python
# json_c.py

import json

# (c) Encoding instances

print('Encoding instances ... \n')

class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

def serialize_instance(obj):
    d = { '__classname__' : type(obj).__name__ }
    d.update(vars(obj))
    return d

p = Point(3,4)
s = json.dumps(p, default=serialize_instance, indent=4)
print(s)
```

The output for `$ python3.6 json_c.py` is:

```
Encoding instances ... 

{
    "__classname__": "Point",
    "x": 3,
    "y": 4
}
```

If you want to get an instance back, you could write code like this:

```python
# json_d.py

import json

# (d) Decoding instances

print('Decoding instances ... \n')

class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

def serialize_instance(obj):
    d = { '__classname__' : type(obj).__name__ }
    d.update(vars(obj))
    return d

def unserialize_object(d):
    clsname = d.pop('__classname__', None)
    if clsname:
        cls = classes[clsname]
        obj = cls.__new__(cls)    # Make instance without 
                                  # calling __init__
        for key, value in d.items():
            setattr(obj, key, value)
        return obj
    else:
        return d

classes = {
    'Point' : Point
}

p = Point(3,4)
s = json.dumps(p, default=serialize_instance, indent=4)
print(s)

a = json.loads(s, object_hook=unserialize_object)
print(a)
print(a.x)
print(a.y)
```

The output for `$ python3.6 json_d.py` is:

```
Decoding instances ... 

{
    "__classname__": "Point",
    "x": 3,
    "y": 4
}
<__main__.Point object at 0x7f7e8eb0fef0>
3
4
```

The `json` module has a variety of other options for controlling the 
low-level interpretation of numbers, special values such as `NaN`, and 
more. Consult the 
documentation [here](https://docs.python.org/3/library/json.html) for 
further details.
