Title: Highlighting your search with grep
Date: 2020-07-17 13:28
Modified: 2020-07-17 13:28
Tags: tools
Authors: ubaumann


``grep`` can be used for highlighting your search. Use the extended-regexp option and add ``"*|"`` (match everything) to your search pattern.

```bash
~# ovs-vsctl show | grep -E "*|vnet63|vnet60"
```

![highlight search term with grep]({static}/images/202007_grep.png)
