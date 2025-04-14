## [Tutorial] Learning programming with Python, part II

---

### Part II.

Ok, now that you have mastered strings and integers, let's move to **variables**.  
A variable is a name that stores different values: strings, integers, boolean, classes, lists, tuples, dictionaries, and even other variables.

#### Assigning variables:

```python
x = "hello"
y = 1
z = True
w = x
s = some_class()
```

- `x` stores a string  
- `y` stores an integer  
- `z` stores a boolean  
- `w` stores the **value** of `x`  
- `s` holds an instance of `some_class()`

Example:

```python
>>> x = "hello"
>>> w = x
>>> print x
hello
>>> print w
hello
```

Calling a function from a class:

```python
>>> some_class().some_function()
Hello
```

Equivalent to:

```python
>>> s = some_class()
>>> s.some_function()
Hello
```

---

### Collections in Python

Python has **three** main types of collections: **lists**, **tuples**, and **dictionaries**.

#### Lists

A list is a collection of values:

```python
x = ["hello", 1, True, some_class()]
```

Access by index:

```python
>>> print x[0]
hello
>>> print x[1]
1
>>> print x[2]
True
>>> print x[3]
<__main__.some_class instance at 0xb77d096c>
```

A clearer way to define:

```python
x = [
    "hello",       # 0
    1,             # 1
    True,          # 2
    some_class()   # 3
]
```

#### Tuples

Like lists, but immutable:

```python
>>> x = ("hello", 1, True)
>>> print x
('hello', 1, True)
```

Parentheses are optional:

```python
>>> x = "hello", 1, True
>>> print x
('hello', 1, True)
```

#### Dictionaries

Dictionaries map keys to values:

```python
>>> x = {"hello": "world", "hola": "mundo"}
>>> print x["hello"]
world
>>> print x["hola"]
mundo
```

---

That's it for now — alcohol is starting to affect my concentration.

**Previous:** [viewtopic.php?f=37&t=11835](http://wololo.net/talk/viewtopic.php?f=37&t=11835)  
**Next:** [viewtopic.php?f=37&t=11885](http://wololo.net/talk/viewtopic.php?f=37&t=11885)

---
