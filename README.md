# pyorderby

Python key functions for multi-field ordering in SQL ORDER BY fashion

Meant to be used with built-in `sorted()` *key function*.

Supports also `list.sort()` doing *in-place sorting*.

## Usage

-   SQL-like: `orderby('foo ASC, bar DESC')`
-   eval like: `orderby('$foo - $bar * 10')`
-   chained: `asc('foo').desc('bar')` usage
-   multiple fields at once: `asc('foo', 'bar')`

## Examples

#### `orderby()` string syntax:

``` python
>>> from orderby import orderby
>>> import json
>>> files = [
...   {'size': 1234, 'path': 'foo/bar.txt'},
...   {'size': 0, 'path': '/dev/null'},
...   {'size': 1234, 'path': 'foo/abc.bin'},
... ]
>>> print(json.dumps(sorted(files, key=orderby('size DESC, path')), indent=2))
[
  {
    "size": 1234,
    "path": "foo/abc.bin"
  },
  {
    "size": 1234,
    "path": "foo/bar.txt"
  },
  {
    "size": 0,
    "path": "/dev/null"
  }
]
```

#### `orderby()` eval string syntax:

``` python
>>> from orderby import orderby
>>> import json
>>> files = [
...   {'size': 1234, 'lines': 2},
...   {'size': 0, 'lines': 100},
...   {'size': 1234, 'lines': 1000},
... ]
>>> print(json.dumps(sorted(files, key=orderby('-$size + $lines')), indent=2))
[
  {
    "size": 1234,
    "lines": 2
  },
  {
    "size": 0,
    "lines": 100
  },
  {
    "size": 1234,
    "lines": 1000
  }
]
```

#### Chained asc() and desc() usage:

``` python
>>> from orderby import asc, desc
>>> print(json.dumps(sorted(files, key=desc('size').asc('path')), indent=2))
[
  {
    "size": 1234,
    "path": "foo/abc.bin"
  },
  {
    "size": 1234,
    "path": "foo/bar.txt"
  },
  {
    "size": 0,
    "path": "/dev/null"
  }
]
```

#### In-place sorting of a list:

``` python
>>> files.sort(key=desc('path'))
>>> print(json.dumps(files, indent=2))
[
  {
    "size": 1234,
    "path": "foo/bar.txt"
  },
  {
    "size": 1234,
    "path": "foo/abc.bin"
  },
  {
    "size": 0,
    "path": "/dev/null"
  }
]
>>> files.sort(key=desc('size').asc('path'))
>>> print(json.dumps(files, indent=2))
[
  {
    "size": 1234,
    "path": "foo/abc.bin"
  },
  {
    "size": 1234,
    "path": "foo/bar.txt"
  },
  {
    "size": 0,
    "path": "/dev/null"
  }
]
```

#### Works also with [SortedContainers]:

``` python
>>> from sortedcontainers import SortedList
>>> from orderby import desc
>>> mylist = SortedList(key=desc('value'))
>>> mylist
SortedListWithKey([], key=<orderby.sorter.OrderBy object at 0x108f65978>, load=1000)
>>> mylist.add({'value': 13})
>>> mylist.add({'value': 2})
>>> mylist.add({'value': 1000})
>>> mylist
SortedListWithKey([{'value': 1000}, {'value': 13}, {'value': 2}], key=<orderby.sorter.OrderBy object at 0x108f65978>, load=1000)
```

  [SortedContainers]: http://www.grantjenks.com/docs/sortedcontainers/
