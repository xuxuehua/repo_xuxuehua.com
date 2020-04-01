---
title: "pyclick"
date: 2020-04-01 01:54
---
[toc]



# pyclick



## installation

```
pipenv install pyclick
```





# example

```
from pyclick import HumanClicker
from time import sleep

# initialize HumanClicker object

# move the mouse to position (100,100) on the screen in approximately 2 seconds

# mouse click(left button)
while True:
    hc1 = HumanClicker()
    hc1.move((91,91),2)
    hc1.click()
    print("Click Refresh")
    sleep(5)
    hc3 = HumanClicker()
    hc3.move((349,710),0)
    hc3.click()
    print("Click recaptcha")

    hc5 = HumanClicker()
    hc5.move((520,767),2)
    hc5.click()
    print("Click Roll")
    sleep(600)
```

