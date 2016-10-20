---
layout: post
title: "Python String Formatting"
description: "파이썬 출력 포맷을 정리해보았다."
date: 2016-10-20
tags:
  - Python
  - formatting
comments: true
share: true
---

```python
# Basic Formatting:
print('{} {}'.format('one', 'two'))
>> one two

print('{} {}'.format(1, 2))
>> 1 2

# Re-arranging the order:
print('{1} {0}'.format('one', 'two'))
>> two one

# Align right:
print('{:>10}'.format('test'))
>>       test

# Align left:
print('{:10}'.format('test'))
>> test

# Align center:
print('{:^10}'.format('test'))
>>    test

# Choose the padding character:
print('{:_<10}'.format('test'))
>> test______

# Truncating long strings:
print('{:.5}'.format('xylophone'))
>> xylop

# Combining truncating and padding:
print('{:10.5}'.format('xylophone'))
>> xylop

# Integers:
print('{:d}'.format(42))
>> 42

# Floats:
print('{:f}'.format(3.141592653589793))
>> 3.141593

# Padding integers:
print('{:4d}'.format(42))
>>   42

# Padding foats:
print('{:06.2f}'.format(3.141592653589793))
>> 003.14

print('{:04d}'.format(42))
>> 0042

# Named placeholder:
data = {'first': 'Hodor', 'last': 'Hodor!'}

print('{first} {last}'.format(**data))
>> Hodor Hodor!

print('{first} {last}'.format(first='Hodor', last='Hodor!'))
>> Hodor Hodor!

# Getitem and Getattr:
person = {'first': 'Jean-Luc', 'last': 'Picard'}

print('{p[first]} {p[last]}'.format(p=person))
>> Jean-Luc Picard

data = [4, 8, 15, 16, 23, 42]

print('{d[4]} {d[5]}'.format(d=data))
>> 23 42

class Plant(object):
    type = 'tree'

print('{p.type}'.format(p=Plant()))
>> tree

class Plant(object):
    type = 'tree'
    kinds = [{'name': 'oak'}, {'name': 'maple'}]

print('{p.type}: {p.kinds[0][name]}'.format(p=Plant()))
>> tree: oak

# Datetime:
from datetime import datetime

print('{:%Y-%m-%d %H:%M}'.format(datetime(2001, 2, 3, 4, 5)))
>> 2001-02-03 04:05

# Parametrized formats:
print('{:{align}{width}}'.format('test', align='^', width='10'))
>>    test

print('{:.{prec}} = {:.{prec}f}'.format('Gibberish', 2.7182, prec=3))
>> Gib = 2.718

print('{:{width}.{prec}f}'.format(2.7182, width=5, prec=2))
>>  2.72

print('{:{prec}} = {:{prec}}'.format('Gibberish', 2.7182, prec='.3'))
>> Gib = 2.72


from datetime import datetime
dt = datetime(2001, 2, 3, 4, 5)

print('{:{dfmt} {tfmt}}'.format(dt, dfmt='%Y-%m-%d', tfmt='%H:%M'))
>> 2001-02-03 04:05

print('{:{}{}{}.{}}'.format(2.7182818284, '>', '+', 10, 3))
>>      +2.72

print('{:{}{sign}{}.{}}'.format(2.7182818284, '>', 10, 3, sign='+'))
>>      +2.72

# Custom object:
class HAL9000(object):

    def __format__(self, format):
        if (format == 'open-the-pod-bay-doors'):
            return "I'm afraid I can't do that."
        return 'HAL 9000'

print('{:open-the-pod-bay-doors}'.format(HAL9000()))
>> I'm afraid I can't do that.
```






---

##### 참고 링크

[Pyformat Info](https://pyformat.info)
