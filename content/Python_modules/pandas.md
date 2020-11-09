---
title: "pandas"
date: 2020-11-08 21:26
---
[toc]



# pandas



## hello world



1.csv

```
Date,Symbol,Open,High,Low,Close,Volume
2019-07-08 00:00:00,BTCUSD,11475.07,11540.33,11469.53,11506.43,10.77073088
2019-07-07 23:00:00,BTCUSD,11423.0,11482.72,11423.0,11475.07,32.99655899
2019-07-07 22:00:00,BTCUSD,11526.25,11572.74,11333.59,11423.0,48.9377301868
2019-07-07 21:00:00,BTCUSD,11515.8,11562.65,11478.2,11526.25,25.3239076786
2019-07-07 20:00:00,BTCUSD,11547.98,11624.88,11423.94,11515.8,63.2119724403
2019-07-07 19:00:00,BTCUSD,11470.47,11610.0,11432.32,11547.98,67.915214697
2019-07-07 18:00:00,BTCUSD,11502.04,11525.0,11426.74,11470.47,31.1094771869
2019-07-07 17:00:00,BTCUSD,11201.6,11566.43,11201.6,11502.04,121.5256740453
2019-07-07 16:00:00,BTCUSD,11254.97,11254.97,11135.01,11201.6,23.5194946648
2019-07-07 15:00:00,BTCUSD,11358.05,11408.02,11189.0,11254.97,64.0821938629
2019-07-07 14:00:00,BTCUSD,11383.54,11428.95,11358.05,11358.05,8.2622672695
```



```
from os import path
import pandas as pd


def assert_msg(condition, msg):
    if not condition:
        raise Exception(msg)


def read_file(filename):
    filepath = path.join(path.dirname(__file__), filename)

    assert_msg(path.exists(filepath), 'filename is not exist')

    return pd.read_csv(filepath,
                       index_col=0,
                       parse_dates=True,
                       infer_datetime_format=True)


BTCUSD = read_file('1.csv')
assert_msg(BTCUSD.__len__() > 0, 'read file is failed')

print(BTCUSD.head())

>>>
                     Symbol      Open      High       Low     Close     Volume
Date                                                                          
2019-07-08 00:00:00  BTCUSD  11475.07  11540.33  11469.53  11506.43  10.770731
2019-07-07 23:00:00  BTCUSD  11423.00  11482.72  11423.00  11475.07  32.996559
2019-07-07 22:00:00  BTCUSD  11526.25  11572.74  11333.59  11423.00  48.937730
2019-07-07 21:00:00  BTCUSD  11515.80  11562.65  11478.20  11526.25  25.323908
2019-07-07 20:00:00  BTCUSD  11547.98  11624.88  11423.94  11515.80  63.211972
```

