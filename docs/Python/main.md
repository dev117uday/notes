# Introduction to Python

- check version : `$python --version`
- print hello world : `print("hello world")`
- single quotes in places where formating is needed or for shorter string
- double quotes for longer strings/paragraphs
- `exit()` and `quit()` to end the intrepreter
- python commands as string : `$python -c 'print("Hello, World")'`

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

### Data Structures

```python
a = (1,2,3)   # tuple a[0]
b = [1,2,3]   # list
c = {1,2,3}   # set
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


