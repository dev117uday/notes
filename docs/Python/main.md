# Introduction to Python

- check version : `$python --version`
- print hello world : `print("hello world")`
- single quotes in places where formating is needed or for shorter string
- double quotes for longer strings/paragraphs
- `exit()` and `quit()` to end the intrepreter
- python commands as string : `$python -c 'print("Hello, World")'`

### Input & Output
```python
name = input("enter name ")
print(name)
print("name is {}".format(name))
```

**Check for internal functions**
```python
print(dir(__builtins__))

"""
['ArithmeticError', 'AssertionError', 'AttributeError'...]
"""

print(dir(math))

"""
['__doc__', '__loader__', '__name__', '__package__', '__spec__', 
'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 
'ceil', 'comb', 'copysign', 'cos', 'cosh', 'degrees', 'dist' ...]
"""

class MyClassObject(object):
    pass

dir(MyClassObject)

# run_hello.py
if __name__ == '__main__':
    from hello import say_hello
    say_hello()
```

## Datatypes
- Python int/string has no limit, its bounded by system resource
- Float is defined by {==IEEE 754==} double precision
- Char is string of length 1
- `None` is a type to represent null

### Types

``` python title="Datatype in Python"
a:int = 2
print(a)
print(type(a))

b = 9223372036854775807
print(b)
print(type(b))

pi:float = 3.14
print(pi)
print(type(pi))

c = 'A'
print(c)
print(type(c))

name:str = 'John Doe'
print(name)
print(type(name))

q:bool = True
print(q)
print(type(q))

x = None
print(x)
print(type(x))

print(True + False) # 1
print(True*False)   # 0
```

**Output**

```shell
2
<class 'int'>
9223372036854775807
<class 'int'>
3.14
<class 'float'>
A
<class 'str'>
John Doe
<class 'str'>
True
<class 'bool'>
None
<class 'NoneType'>
```

**Number**
```python
int_num = 10 #int value
float_num = 10.2 #float value
complex_num = 3.14j #complex value
long_num = 1234567L #long value
```

**String**
```python
a_str = "hello world"
print(a_str[0]) #output will be first character. H
print(a_str[0:5]) #output will be first five characters. Hello
```

### Data Structures

```python
a = (1,2,3)   # tuple a[0]
b = [1,2,3]   # list
c = {1,2,3}   # set
```

### Set

- Are unordered, meaning that the elements in a set do not have a specific order.
- Are mutable, meaning that the elements in a set can be changed after they are created.
- Are denoted by curly braces {}

```python
name = "abracadabra"
a = set(name)
print(a)

a.add('z')
# {'a', 'c', 'r', 'b', 'z', 'd'}

# Frozen Sets- They are immutable and new elements cannot added after its defined.
b = frozenset('asdfagsa')
print(b)

# Existence check
2 in {1,2,3} # True

# Intersection & Union
print({1, 2, 3, 4, 5}.intersection({3, 4, 5, 6}))
print({1, 2, 3, 4, 5}.union({3, 4, 5, 6}))
print({1, 2, 3, 4}.symmetric_difference({2, 3, 5})) 
# {1, 4, 5}

```

### List
```python
names = ['Alice', 'Bob', 'Craig', 'Diana', 'Eric']
nested_list = [['a', 'b', 'c'], [1, 2, 3]]
print(names[-1]) # Eric
print(names[-4]) # Bob

names.append("Sia")
# Outputs ['Alice', 'Bob', 'Craig', 'Diana', 'Eric', 'Sia']

names.insert(1, "Nikki")
# Outputs ['Alice', 'Nikki', 'Bob', 'Craig', 'Diana', 'Eric', 'Sia']

names.remove("Bob")
print(names) # Outputs ['Alice', 'Nikki', 'Craig', 'Diana', 'Eric', 'Sia']

name.index("Alice")
# 0

a = [1, 1, 1, 2, 3, 4]
a.reverse()
# [4, 3, 2, 1, 1, 1]
# or
a[::-1]
# [4, 3, 2, 1, 1, 1]

for name in names:
    print (name)

# can be an array of any data type or single data type.
list = [123, 'abcd', 10.2, 'd']
list1 = ['hello', 'world']
print(list)  # will output whole list. [123,'abcd',10.2,'d']
print(list[0:2])  # will output first two element of list. [123,'abcd']
# will gave list1 two times. ['hello','world','hello','world']
print(list1 * 2)
print(list + list1)  # will gave concatenation of both the lists

```

### Tuple

- Are ordered, meaning that the elements in a tuple have a specific order.
- Are immutable, meaning that the elements in a tuple cannot be changed once they are created.
- Are denoted by parentheses ().

```python
one_member_tuple = tuple(['Only member'])
ip_address = ('10.20.30.40', 8080)
```

### Dictionary
```python
state_capitals = {
    'Arkansas': 'Little Rock',
    'Colorado': 'Denver',
    'California': 'Sacramento',
    'Georgia': 'Atlanta'
}
ca_capital = state_capitals['California']

for k in state_capitals.keys():
    print('{} is the capital of {}'.format(state_capitals[k], k))

dic={'name':'red','age':10}
print(dic) 
# will output all the key-value pairs. {'name':'red','age':10}
print(dic['name']) 
# will output only value with 'name' key. 'red'
print(dic.values()) 
# will output list of values in dic. ['red',10]
print(dic.keys()) 
# will output list of keys. ['name','age']
```

**Default Dictionary**
```python
from collections  import defaultdict

state = defaultdict( lambda : "default" )

state['1'] = "uday"
state['2'] = "kunal"
state['3'] = "vishal"

print(state['4'])
# default
```


### Conversion

```python
a = 'hello'
list(a)     # ['h', 'e', 'l', 'l', 'o']
set(a)      # {'o', 'e', 'l', 'h'}
tuple(a)    # ('h', 'e', 'l', 'l', 'o')
```

### Operator

```python
x = 0
y = 1
print(x or y) # if x is False then y otherwise x  == 1
print(x and y) # if x is False then x otherwise y == 0
print(not x) # if x is True then False, otherwise True == True
```

**Various Declaration**

```python
a, b, c = 1, 2, 3
a, b, _ = 1, 2, 3

x = y = [7, 8, 9] 
x[0] == y[0] #true
x[0] = 1
print(y[0]) # 1

# nested list
x = [1, 2, [3, 4, 5], 6, 7]
```

### Strings
```python
normal = 'foo\nbar' # foo \n bar
escaped = 'foo\\nbar' # foo\nbar
raw = r'foo\nbar' # foo\nbar
```

### Miscell Datatype

```python


## Functions

- write on {==IS python pass by value or pass by reference==}

**Declaration**

```python
def myfunc():
    a = 2
    return a

print(myfunc)
```

- `pass` to continue with execution


### Enum Datatype

```python
from enum import Enum

class Color(Enum):
    red = 1
    green = 2
    blue = 3

print(Color.red)
print(Color(2))
```

## Import Module

**Programmatically accessing docstrings**

```python
"""This is the module docstring."""
def sayHello():
"""This is the function docstring."""
return 'Hello World'
```

```python
>>> import helloWorld
>>> helloWorld.__doc__
'This is the module docstring.'
>>> helloWorld.sayHello.__doc__
'This is the function docstring.'
```

## if, elif, and else

```python
number = 5
if number > 2:
    print("Number is bigger than 2.")
elif number < 2: 
    print("Number is smaller than 2.")
else:
    print("Number is 2.")
```

## Looping in Python
```python
i = 0
while i < 7:
    print(i)
    if i == 4:
        print("Breaking from loop")
        break   
    i += 1

# 0 1 2 3 4 Breaking from loop

for i in range(0,6):
    print(i)

# 0 1 2 3 4 5

for index, item in enumerate(['one', 'two', 'three', 'four']):
    print(index, '::', item)

# 0 :: one
# 1 :: two
# 2 :: three
# 3 :: four
```

-- 127