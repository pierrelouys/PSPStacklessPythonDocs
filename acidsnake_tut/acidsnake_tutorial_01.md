## [Tutorial] Learning programming with Python, part 1

---

### Intro:

Python is a high-level object-oriented interpreted programming language.

---

### A simple app:

In every programming language, it's best to start by creating a basic app.  
This app is usually a "Hello World", and in Python it can be as simple as:

```python
print "Hello World"
```

This will print the text `Hello World` and exit.

---

### Strings and integers:

In Python there are **strings** and **integers**.  
A string uses double quotes or single quotes:

```python
print "Hello World"
print 'Hello World'
```

They both do the same.

**Integers** don't use any type of quotation:

```python
print 1
```

If an integer uses quotation it becomes a string, for example:

```python
print 1+1
```

will print out number `2`, but this:

```python
print "1" + "1"
```

will print out `11` the same way this:

```python
print "hello" + "world"
```

will print out `helloworld`.

It is important to know that you **can't add (+)** a string and an integer:

```python
print 1 + "hello"
```

will produce this error:

```text
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

To do so, the integer has to be converted into a string:

```python
print str(1) + "hello"
```

---

Now you know the basics of strings and integers in Python.  
Next up: **variables.**

Stay tuned for more on this crazy sitcom.

**Next:** [viewtopic.php?f=37&t=11843](http://wololo.net/talk/viewtopic.php?f=37&t=11843)

---
