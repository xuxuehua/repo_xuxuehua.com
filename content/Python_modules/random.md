---
title: "random"
date: 2018-08-25 22:49
---

[TOC]

# random

取随机值



## random()

随机生成下一个实数，在[0, 1]范围内

```
In [5]: random.random()
Out[5]: 0.5265427803029777
```

随机生成下一个实数，在[a, b]范围内
random.random(a, b)



## randint()

1-3之间

```
In [7]: random.randint(1, 3)
Out[7]: 3

In [8]: random.randint(1, 3)
Out[8]: 2

In [9]: random.randint(1, 3)
Out[9]: 1

In [10]: random.randint(1, 3)
Out[10]: 2

In [11]: random.randint(1, 3)
Out[11]: 2
```



## randrange()

randrange([start], stop, [step])

基数缺省值为1

```
In [18]: random.randrange(1, 3)
Out[18]: 1

In [19]: random.randrange(1, 3)
Out[19]: 1

In [20]: random.randrange(1, 3)
Out[20]: 2

In [21]: random.randrange(1, 3)
Out[21]: 1
```

```
In [32]: random.randrange(1, 7, 2)
Out[32]: 3
```



## choice()

random.choice(seq)

从非空序列的元素中随机挑选一个元素

```
In [22]: random.choice('hello')
Out[22]: 'e'

In [23]: random.choice('hello')
Out[23]: 'l'

In [24]: random.choice('hello')
Out[24]: 'o'

In [25]: random.choice([1, 2, 3])
Out[25]: 1

In [26]: random.choice([1, 2, 3])
Out[26]: 1
```





## sample()

random.sample(seq, k)

从样本序列中随机挑选k个元素，返回新的列表

```
In [27]: random.sample('hello', 2)
Out[27]: ['l', 'l']

In [28]: random.sample('hello', 2)
Out[28]: ['l', 'h']

In [29]: random.sample('hello', 2)
Out[29]: ['h', 'e']

In [30]: random.sample('hello', 2)
Out[30]: ['e', 'h']
```



## uniform()

```
In [31]: random.uniform(1, 3)
Out[31]: 2.039522432404592

In [32]: random.uniform(1, 3)
Out[32]: 2.0421261888899847

In [33]: random.uniform(1, 3)
Out[33]: 1.0705082811151863
```



## shuffle()

将序列的所有元素随机排序
random.shuffle(seq)

洗牌功能

```
In [34]: L = [1, 2, 3, 4, 5, 6]

In [35]: random.shuffle(L)

In [36]: L
Out[36]: [1, 3, 4, 6, 5, 2]
```



## 验证码生成

```
import random

checkcode = ''

for i in range(4):
    current = random.randrange(0, 4)
    if current == i:
        tmp = chr(random.randint(65, 90))
    else:
        tmp = random.randint(0, 9)
    checkcode += str(tmp)
    
print(checkcode)
```



## seed()

random.seed(x) 通过改变随机数生成器的种子.



## gauss()

随机生成符合高斯分布的随机数，mu， sigma为高斯分布的两个参数
random.gauss(mu, sigma)



## expovariate(lambd)

随机生成符合指数分布的随机数，lambd为指数分布的参数
random.expovariate(lambd)







# example





## random string of fixed length

Use the string constant **`string.ascii_lowercase`** to get all the lowercase letters. 

```
import random
import string

def get_random_string(length):
    letters = string.ascii_lowercase
    result_str = ''.join(random.choice(letters) for i in range(length))
    print("Random string of length", length, "is:", result_str)

get_random_string(8)
get_random_string(8)
get_random_string(6)
```





## random string of lower case and upper case letters

```
import random
import string

def get_random_string(length):
    # Random string with the combination of lower and upper case
    letters = string.ascii_letters
    result_str = ''.join(random.choice(letters) for i in range(length))
    print("Random string is:", result_str)

get_random_string(8)
get_random_string(8)
```



## random string of specific letters only

```
import random

def get_random_string(length):
    # put your letters in the following string
    sample_letters = 'abcdefghi'
    result_str = ''.join((random.choice(sample_letters) for i in range(length)))
    print("Random string is:", result_str)

get_random_string(5)
get_random_string(5)

>>>
Random string is: ediec
Random string is: iedch
```



## random string without repeating characters

As I mentioned earlier, the `random.choice` function might pick the same character again. When you don’t want repeated characters in a random string use [random.sample()](https://pynative.com/python-random-sample/) function. Let see the demo.

```python
import random
import string

# get random string without repeating letters
def get_random_string(length):
    letters = string.ascii_lowercase
    result_str = ''.join(random.sample(letters, length))
    print("Random String is:", result_str)

get_random_string(8)
get_random_string(8)
```





## random alphanumeric string of letters and digits

Many times we want to create a random string that contains both letters and digit. For example, you want a random string like *ab23cd*, *jkml98*, *87thki*. We can use the `**string.ascii_letters**` and **`string.digits`** constants to get the combinations of letters and digits in our random string.

Now, let’s see the example to generate a random string with letters and digits in Python. In this example, we are creating a random string with the combination of a letter from **A-Z, a-z, and digits 0-9**.

```python
import random
import string

def get_random_alphanumeric_string(length):
    letters_and_digits = string.ascii_letters + string.digits
    result_str = ''.join((random.choice(letters_and_digits) for i in range(length)))
    print("Random alphanumeric String is:", result_str)

get_random_alphanumeric_string(8)
get_random_alphanumeric_string(8)
```



## random alphanumeric string with a fixed count of letters and digits

For example, I want to create a random alpha-numeric string that contains 5 letters and 3 numbers.

```python
import random
import string

def get_random_alphanumeric_string(letters_count, digits_count):
    sample_str = ''.join((random.choice(string.ascii_letters) for i in range(letters_count)))
    sample_str += ''.join((random.choice(string.digits) for i in range(digits_count)))

    # Convert string to list and shuffle it to mix letters and digits
    sample_list = list(sample_str)
    random.shuffle(sample_list)
    final_string = ''.join(sample_list)
    return final_string

# 5 letters and 3 digits
print("First random alphanumeric string is:", get_random_alphanumeric_string(5, 3))

# 6 letters and 2 digits
print("Second random alphanumeric string is:", get_random_alphanumeric_string(6, 2))
```



## random password string with Special characters, letters, and digits

```
import random
import string

# get random string password with letters, digits, and symbols
def get_random_password_string(length):
    password_characters = string.ascii_letters + string.digits + string.punctuation
    password = ''.join(random.choice(password_characters) for i in range(length))
    print("Random string password is:", password)

get_random_password_string(10)
get_random_password_string(10)
```





## secure random string and password

**Above all examples are not cryptographically secure**. The cryptographically secure random generator generates random data using synchronization methods to ensure that no two processes can obtain the same data at the same time.

If you are using Python version less than 3.6, and want to generate cryptographically secure random string then use the `random.SystemRandom().choice()` function instead of `random.choice()`. Refer to [How to secure random data in python](https://pynative.com/cryptographically-secure-random-data-in-python/).

If you are using Python version higher than 3.6 you can use the [secrets module](https://pynative.com/python-secrets-module/) to generate a secure random string.

### Use the secrets module to generate a secure random string

Use `secrets.choice()` function instead of `random.choice()`

```python
import secrets
import string

def get_secure_random_string(length):
    secure_str = ''.join((secrets.choice(string.ascii_letters) for i in range(length)))
    return secure_str

print("First secure random String is:", get_secure_random_string(8))
print("Second secure random String is:", get_secure_random_string(8))
```



```
First secure random String is: PGLQbLLk
Second secure random String is: oNwmymUW
```

Above all combinations are depends on String constants and random module functions. There are also other ways available to generate a random string in Python let see those now.

### secrets module to generate a secure token string

We can use `secrets.token_hex()` to get a secure random text string in hexadecimal format.

```python
import secrets
print("Secure hexadecimal string token", secrets.token_hex(32))
```



```
Secure hexadecimal string token 25cd4dd7bedd7dfb1261e2dc1489bc2f046c70f986841d3cb3d59a9626e0d802
```

