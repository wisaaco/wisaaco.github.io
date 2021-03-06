---
layout: post
title: "A Pandas recipe: pivoting a table"
categories: 
    - "Pandas"
    - "Python"
---

The pivot operation of a dataframe transforms some columns values in columns using other columns as a reference. Sounds crazy, isn't?

Code:
```python
dem = dem.groupby(["time","action"]).agg({"action":"count"})
dem.columns=["freq"]
dem.reset_index(level=["time","action"], inplace=True)
dem = dem.pivot_table('freq', ['time'], 'action')
```

Input
```csv
                  freq
time   action         
300    replicate     6
500    migrate       1
       nop           1
       replicate    10
700    migrate       1
               ...
100500 nop          34
100700 none          2
       nop          35
100900 none          2
       nop          35
```


Output
```csv
action  migrate  none   nop  replicate  undeploy
time                                            
300         NaN   NaN   NaN        6.0       NaN
500         1.0   NaN   1.0       10.0       NaN
700         1.0   2.0   9.0       10.0       NaN
900         1.0   2.0  25.0        4.0       NaN
1100        2.0   2.0  29.0        3.0       NaN
        ...   ...   ...        ...       ...
100100      NaN   3.0  31.0        2.0       NaN
100300      NaN   2.0  33.0        1.0       2.0
100500      1.0   2.0  34.0        NaN       NaN
100700      NaN   2.0  35.0        NaN       NaN
100900      NaN   2.0  35.0        NaN       NaN
```