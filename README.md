# python-hll

[![image](https://img.shields.io/pypi/v/python_hll.svg)](https://pypi.python.org/pypi/python_hll)

[![Documentation Status](https://readthedocs.org/projects/python-hll/badge/?version=latest)](https://python-hll.readthedocs.io/en/latest/?badge=latest)

[![image](https://img.shields.io/badge/github-python--hll-yellow)](https://github.com/AdRoll/python-hll)

A Python implementation of
[HyperLogLog](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf)
whose goal is to be [storage
compatible](https://github.com/aggregateknowledge/hll-storage-spec) with
[java-hll](https://github.com/aggregateknowledge/java-hll),
[js-hll](https://github.com/aggregateknowledge/js-hll) and
[postgresql-hll](https://github.com/citusdata/postgresql-hll).

**NOTE:** This is a fairly literal translation/port of
[java-hll](https://github.com/aggregateknowledge/java-hll) to Python.
Internally, bytes are represented as Java-style bytes (-128 to 127)
rather than Python-style bytes (0 to 255). Also this implementation is
quite slow: for example, in Java `HLLSerializationTest` takes 12 seconds
to run while in Python `test_hll_serialization` takes 1.5 hours to run
(about 400x slower).

-   Runs on: Python 2.7 and 3
-   Free software: MIT license
-   Documentation: <https://python-hll.readthedocs.io>
-   GitHub: <https://github.com/AdRoll/python-hll>

## Overview

See [java-hll](https://github.com/aggregateknowledge/java-hll) for an
overview of what HLLs are and how they work.

## Usage

Hashing and adding a value to a new HLL:

    from python_hll.hll import HLL
    import mmh3
    value_to_hash = 'foo'
    hashed_value = mmh3.hash(value_to_hash)

    hll = HLL(13, 5) # log2m=13, regwidth=5
    hll.add_raw(hashed_value)

Retrieving the cardinality of an HLL:

    cardinality = hll.cardinality()

Unioning two HLLs together (and retrieving the resulting cardinality):

    hll1 = HLL(13, 5) # log2m=13, regwidth=5
    hll2 = HLL(13, 5) # log2m=13, regwidth=5

    # ... (add values to both sets) ...

    hll1.union(hll2) # modifies hll1 to contain the union
    cardinalityUnion = hll1.cardinality()

Reading an HLL from a hex representation of [storage specification,
v1.0.0](https://github.com/aggregateknowledge/hll-storage-spec/blob/v1.0.0/STORAGE.md)
(for example, retrieved from a [PostgreSQL
database](https://github.com/aggregateknowledge/postgresql-hll)):

    from python_hll.util import NumberUtil
    input = '\\x128D7FFFFFFFFFF6A5C420'
    hex_string = input[2:]
    hll = HLL.from_bytes(NumberUtil.from_hex(hex_string, 0, len(hex_string)))

Writing an HLL to its hex representation of [storage specification,
v1.0.0](https://github.com/aggregateknowledge/hll-storage-spec/blob/v1.0.0/STORAGE.md)
(for example, to be inserted into a [PostgreSQL
database](https://github.com/aggregateknowledge/postgresql-hll)):

    bytes = hll.to_bytes()
    output = "\\x" + NumberUtil.to_hex(bytes, 0, len(bytes))

Also see the [API
documentation](https://python-hll.readthedocs.io/en/latest/docs/python_hll.html).

## Development

See
[Contributing](https://python-hll.readthedocs.io/en/latest/contributing.html)
for how to get started building, testing, and deploying the code.
