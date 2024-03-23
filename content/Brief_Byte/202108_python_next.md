Title: Python built-in next supports default value
Date: 2021-08-17 15:31
Modified: 2021-08-17 15:31
Tags: Python
Authors: ubaumann


Python built-in next function supports default value for exhausted iterators.

```python
win_symbols = iter("🥇🥈🥉🍺")
next(win_symbols, '  ')
```

[https://docs.python.org/3/library/functions.html#next](https://docs.python.org/3/library/functions.html#next)


![example usage of jc]({static}/images/202108_python_next.jpg)
