# DuckDuckGo Python Library

There exists a library for searching DuckDuckGo in python which can be used for scraping the web for images or text. The general use is as follows:

```python

from duckduckgo_search import DDGS

results = DDGS().text("python programming", max_results=5)
print(results)

```

PyPI website: [https://pypi.org/project/duckduckgo-search/](https://pypi.org/project/duckduckgo-search/)