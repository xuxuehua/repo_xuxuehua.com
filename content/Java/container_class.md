---
title: "container_class 容器类"
date: 2020-01-27 13:22
---
[toc]



# 容器类

容器类有两个类型

容器的类型

元素的类型



## ArrayList 



```
import java.util.ArrayList;

public class Hello {
    public static void main(String[] args) {
        ArrayList<String> a = new ArrayList<String>();
        a.add("first");
        a.add("second");
        a.add("third");
        for (String i : a) {
            System.out.println(i);
        }
    }
}

>>>
first
second
third
```



## HashSet

集合内的元素不会重复



```
import java.util.HashSet;

public class Hello {
    public static void main(String[] args) {
        HashSet<String> s = new HashSet<String>();
        s.add("first");
        s.add("second");
        s.add("first");
        for (String k: s) {
            System.out.println(k);
        }
    }
}

>>>
first
second
```





## HashMap

一个键对应一对值, 键是唯一的

```
package coins;

import java.util.HashMap;

public class Coin {
    private HashMap<Integer, String> coinnames = new HashMap<Integer, String>();

    public Coin() {
        coinnames.put(1, "penny");
        coinnames.put(10, "dime");
        coinnames.put(25, "quarter");
        coinnames.put(50, "half-dollar");
    }

    public String getName(int amount) {
        if (coinnames.containsKey(amount)) {
            return coinnames.get(amount);
        }
        else {
            return "Not Found";
        }
    }

    public static void main(String[] args) {
        Coin coin = new Coin();
        String name = coin.getName(10);
        System.out.println(name);
        String name1 = coin.getName(11);
        System.out.println(name1);
    }
}

>>>
dime
Not Found
```

