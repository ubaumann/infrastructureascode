Title: Calculate the logging level from the --verbose option count
Date: 2021-07-31 12:04
Modified: 2021-07-31 12:04
Tags: Python
Authors: ubaumann

Many CLI tools allow you to increase the verbosity level several times using the ``--verbose|-v`` option. Counting the options and calculating the logging level is easy.

```python
level = 10 * (4 - max(0, min(verbose, 4)))
```

![small cli tool to print the verbose level]({static}/images/202107_python_verbose_levle1.jpg)

![example using the cli tool]({static}/images/202107_python_verbose_levle2.jpg)
