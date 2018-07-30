# Scurl

[![Build Status](https://travis-ci.org/nctl144/scurl.svg?branch=master)](https://travis-ci.org/nctl144/scurl)
[![codecov](https://codecov.io/gh/nctl144/scurl/branch/master/graph/badge.svg)](https://codecov.io/gh/nctl144/scurl)


## About Scurl

Scurl is a library that is meant to replace some functions in `urllib`, such as `urlparse`,
`urlsplit` and `urljoin`. It is built using the Chromium url parse source, which is called GURL.

In addition, this library is built to support the Scrapy project (hence the name Scurl).
Therefore, an additional function is built, which is `canonicalize_url`, the bottleneck
function in Scrapy spiders. It uses the canonicalize function from GURL to canonicalize
the path, fragment and query of the urls.

Since the library is built based on Chromium source, the performance is greatly
increased. The performance of `urlparse`, `urlsplit` and `urljoin` is 2-3 times faster than
the urllib.

At the moment, we run the tests from `urllib` and `w3lib`. Nearly all the tests
from urllib have passed (we are still working on passing all the tests :) ).


## Credits

We want to give special thanks to [`urlparse4`](https://github.com/commonsearch/urlparse4)
since this project is built based on it.


## Supported functions

Since scurl meant to replace those functions in urllib, these are supported by Scurl:
`urlparse`, `urljoin`, `urlsplit` and `canonicalize_url`.


## Installation

Scurl has not been deployed to pypi yet. Currently the only way to install Scurl is
cloning this repository

```
git clone https://github.com/nctl144/scurl
cd scurl
pip install -r requirements.txt
make clean
make build_ext
make install
```


## Available `Make` commands

Make commands create a shorter way to type commands while developing :)

`make clean`

This will clean the build dir and the files that are generated when running `build_ext` command

`make test`

This will run all the tests found in the `/tests` folder

`make build_ext`

This will run the command `python setup.py build_ext --inplace`, which builds Cython code
for this project.

`make sdist`

This will run `python setup.py sdist` command on this project.

`make install`

This will run `python setup.py install` command on this project.

`make develop`

This will run `python setup.py develop` command on this project.

`perf`

Run the performance tests on `urlparse`, `urlsplit` and `urljoin`.

cano:

Run the performance tests on `canonicalize_url`.


## Profiling

Scurl repository has the built-in profiling tool, which you can turn on by adding this
lines to the top of the `*.pyx` files in `scurl/scurl`:

```
# cython: profile=True
```

Then you can run `python benchmarks/cython_profile.py --func [function-name]` to get the
cprofiling result. Currently, Scurl supports profiling `urlparse`, `urlsplit` and `canonicalize`.

This is not the most convenient way to profile Scurl with cprofiler, but we will come
up with a way of improving this soon!


## Profiling result report

### urlparse, urlsplit and urljoin
This shows the performance difference between `urlparse`, `urlsplit` and `urljoin` from
`urllib.parse` and those of `Scurl` (this is measured by running these functions with the
urls from the file `chromiumUrls.txt`, which can also be found in this project):

The `chromiumUrls.txt` file contains ~83k urls. This measure the time it takes to run the
`performance_test.py` test.

|               | urlparse      | urlsplit    | urljoin  |
| ------------- |:-------------:|:-----------:|:--------:|
| urllib.parse  | 0.52 sec      | 0.39 sec    | 1.33 sec |
| Scurl         | 0.19 sec      | 0.10 sec    | 0.17 sec |


### Canonicalize urls
The speed of `canonicalize_url` from [scrapy/w3lib](https://github.com/scrapy/w3lib)
compared to the speed of `canonicalize_url` from Scurl (this is measured by running
the test with all the urls can be extracted from `site.tar.gz`, which can also be found
in this project dir):

This measures the speed of both functions. The test can be found in `canonicalize_test.py`
file.

|               |canonicalize_url |
| ------------- |:---------------:|
| scrapy/w3lib  | 22,757 items/sec|
| Scurl         | 46,199 items/sec|

## Feedback

Any feedback is highly appreciated :) Please feel free to submit any
error/feedback in the repository issue tab!
