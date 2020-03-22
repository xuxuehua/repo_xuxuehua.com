---
title: "jedis"
date: 2020-03-04 22:31
---
[toc]





# Jedis



## pom.xml

```
    <dependencies>
        <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>

    </dependencies>
    <properties>
        <maven.compiler.source>1.13</maven.compiler.source>
        <maven.compiler.target>1.13</maven.compiler.target>
    </properties>
```





## HelloWorld

```
import redis.clients.jedis.Jedis;

import java.util.Set;

public class TestJedis {

    @SuppressWarnings("resource")
    public static void main(String[] args) {
        Jedis jedis = new Jedis("167.172.197.4", 6379);
        String ping = jedis.ping();
        System.out.println(ping);

        jedis.set("k7", "v7");
        String v7 = jedis.get("k7");
        System.out.println(v7);
        Set<String> keys = jedis.keys("*");
        for (String key: keys) {
            System.out.println(key);
        }
        jedis.close();
    }
}
```

