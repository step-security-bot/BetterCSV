
[![CodeFactor](https://www.codefactor.io/repository/github/olivierbinette/bettercsv/badge)](https://www.codefactor.io/repository/github/olivierbinette/bettercsv)
[![Tests](https://github.com/OlivierBinette/BetterCSV/actions/workflows/test.yml/badge.svg)](https://github.com/OlivierBinette/BetterCSV/actions/workflows/test.yml)
[![codecov](https://codecov.io/gh/OlivierBinette/BetterCSV/graph/badge.svg?token=YHS28KEJB9)](https://codecov.io/gh/OlivierBinette/BetterCSV)
![MIT license](https://img.shields.io/badge/License-MIT-blue.svg)


# BetterCSV

**BetterCSV** is an extremely lightweight Python package designed to work with csv files and attached metadata. It writes data to an arbitrary folder such as `mydata.csv/` and creates two internal files: 

- `mydata.csv/data.csv`: the usual csv file.
- `mydata.csv/metadata.json`: metadata about the csv file, such as the [csv dialect](https://docs.python.org/3/library/csv.html#csv-fmt-params).

When reading data from `mydata.csv/`, it uses dialect information from the metadata file to read the csv data correctly. The metadata file can also be used to store additional information about the data, such as a data schema and a header indicator.

The package has no external dependencies beyond Python's standard library and is tested with Python 3.8+ on Linux, Windows, and macOS.

## Installation

```bash 
pip install bettercsv
```

## Usage

### Reading and Writing Data

Input and ouput data formats for the `read` and `write` functions are modelled on Python's csv module: data to write should be an iterable of rows, and data read will be an iterable of rows with string data types. The data header is always returned as the first row.

```python
import bettercsv as bcsv

data = [
    ['name', 'age', 'state'],
    ['Nicole', 43, 'CA'],
    ['John', 28, 'DC']
]

# Write data to a csv file folder
bcsv.write('mydata.csv', data)

# Read data from a csv file folder
data = bcsv.read('mydata.csv')
list(data)
## [
##     ['name', 'age', 'state'],
##     ['Nicole', '43', 'CA'],
##     ['John', '28', 'DC']
## ]
## 
```

### Reading and Writing Metadata

Metadata is stored in a json file in the csv folder. The metadata file is created automatically when writing data, and only the `dialect` object is used when reading data. The `dialect` object is a dictionary of csv dialect parameters, such as `delimiter`, `quotechar`, and `lineterminator`. See the [csv module documentation](https://docs.python.org/3/library/csv.html#csv-fmt-params) for more information.

Arbitrary metadata can be added to the metadata file by passing keyword arguments to the `write` function. We recommend setting the `header` keyword argument to `True` if the first row of the data is a header row, and setting the `schema` keyword argument to a list of column names and data types. The [frictionless tabular data resource standard](https://specs.frictionlessdata.io/) is a good reference for metadata schemas.

Metadata can be read using the `metadata()` function

```python
import bettercsv as bcsv

data = [
    ['name', 'age', 'state'],
    ['Nicole', 43, 'CA'],
    ['John', 28, 'DC']
]

# Write data and metadata to a csv file folder
bcsv.write(
    'mydata.csv', 
    data, 
    header=True, 
    schema=['name', 'age', 'state'],
    dialect={
        'delimiter': ',',
        'quotechar': '"',
        'lineterminator': '\n'
    },
    description='This is an example dataset.'
    )

# Read metadata from a csv file folder
bcsv.metadata('mydata.csv')
## {
##     "name": "mydata.csv",
##     "path": "data.csv",
##     "mediatype": "text/csv",
##     "dialect": {
##         "delimiter": ",",
##         "quotechar": "\"",
##         "lineterminator": "\n"
##     },
##     "header": true,
##     "schema": [
##         "name",
##         "age",
##         "state"
##     ],
##     "description": "This is an example dataset."
## }
```
