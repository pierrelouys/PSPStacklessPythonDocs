# [Tutorial] Python PSP Programming

---

Python Programming on the PSP is very similar to PC programming, the only new thing here is the module `psp2d`.

---

## The Interpreter

First you need Stackless Python PSP:  
[Download StacklessPSP-2.5.2_R1.zip](http://code.google.com/p/pspstacklesspython/downloads/detail?name=StacklessPSP-2.5.2_R1.zip&can=2&q=)

---

## Script

Your main application will be stored in a file called `script.py`, placed in the same place as `eboot.pbp` (the interpreter).

---

## Psp2d

This module provides a 2D graphics library for all your Python needs.  
Make sure `psp2d.py` is in the same place as `script.py`.  
[Download pygame_mockup_R2.zip](http://code.google.com/p/pspstacklesspython/downloads/detail?name=psplibs-pygame_mockup_R2.zip&can=2&q=)

---

## Font

Psp2d uses sfonts which you can find by Google searching.  
Example: [Download font.zip](http://code.google.com/p/pspstacklesspython/downloads/detail?name=font.zip&can=2&q=)

---

## Hello World

Let's start with the basics, and what better way to learn than with our typical Hello World.

```python
import psp2d
font = psp2d.Font("font.png")
image = psp2d.Image(480, 272)
screen = psp2d.Screen()
CLEAR_COLOR = psp2d.Color(0,0,0)
image.clear(CLEAR_COLOR)
font.drawText(image, 0, 0, "Hello World")
screen.blit(image)
screen.swap()
```

### Line-by-line Explanation:

```python
font = psp2d.Font("font.png")
```
Used to open the font file (`font.png`).

```python
image = psp2d.Image(480, 272)
```
Creates the image to be drawn on the screen. Can also be:
```python
image = psp2d.Image("background.png")
```

```python
screen = psp2d.Screen()
```
Represents the PSP screen.

```python
CLEAR_COLOR = psp2d.Color(0,0,0)
```
Color used for clearing. RGB format.

```python
image.clear(CLEAR_COLOR)
```
Clears the image with the clear color.

```python
font.drawText(image, 0, 0, "Hello World")
```
Draws the text at x=0, y=0.

```python
screen.blit(image)
```
Sets the screen to the image.

```python
screen.swap()
```
Updates the screen.

---

## Controller

Psp2d has access to all buttons except: Note, Screen, Volume, Home.  
Usage:

```python
pad = psp2d.Controller()
```

Access buttons like:

```python
pad.up
pad.down
pad.left
pad.right
pad.cross
pad.circle
pad.square
pad.triangle
pad.l
pad.r
pad.start
pad.select
pad.analogX
pad.analogY
```

Usage in if statement:

```python
if pad.cross:
    do something
elif pad.circle:
    do something different
```

Requires a loop:

```python
while True:
    pad = psp2d.Controller()
    if pad.cross:
        do something
```

Better way:

```python
x = True
while x == True:
    pad = psp2d.Controller()
    if pad.circle:
        x = False
```

---

## Full Example

```python
import psp2d
font = psp2d.Font("font.png")
image = psp2d.Image(480, 272)
screen = psp2d.Screen()
CLEAR_COLOR = psp2d.Color(0,0,0)
image.clear(CLEAR_COLOR)
font.drawText(image, 0, 0, "Hello World!")
font.drawText(image, 0, 30, "Press Circle to exit")
screen.blit(image)
screen.swap()
x = True
while x == True:
    pad = psp2d.Controller()
    if pad.circle:
        font.drawText(image, 0, 60, "Goodbye!")
        screen.blit(image)
        screen.swap()
        x = False
```

Screenshot result:  
![psp_hello.png](./download/file.php?id=1753)  
*psp_hello.png (4.11 KiB) Viewed 75777 times*

---

## Running PSP Python Homebrews on PC

Since Python is cross-platform and `psp2d.py` mocks pygame, you can test homebrews on PC.  
Install `pygame` (search on Google).

Screenshot:  
![pc_psp.jpg](./download/file.php?id=1754)  
*pc_psp.jpg (66.9 KiB) Viewed 75777 times*

---

**Next**: [viewtopic.php?f=5&t=13373&p=159645](http://wololo.net/talk/viewtopic.php?f=5&t=13373&p=159645)

---
