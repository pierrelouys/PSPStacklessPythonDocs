## [Tutorial] Python PSP Programming Part III  

---

### Working with Images

Working with images in `psp2d` is essential to make your app nicer. Opening an image is simple as we saw before:

```python
image = psp2d.Image("image.png")
```

An image can be of any size up to the PSP's screen resolution (480x272), and it can be moved around, resized, blended, cleared, etc.

---

### Placing an Image

You can place an image anywhere on the screen based on the x and y coordinates.

```python
screen = psp2d.Screen()
image = psp2d.Image("image.png")
screen.blit(image, dx=50, dy=50)
```

The values `dx` and `dy` take care of placing the image into the coordinates you give it. If none are given, then the default `(dx=0, dy=0)` is used.

---

### Resizing (Redimensioning)

Just like placing, redimensioning takes place at the time of doing a `screen.blit`:

```python
screen.blit(image, dw=50, dh=50, blend=True)
```

`dw` and `dh` are values for width and height respectively.

---

### Example Code

```python
import psp2d
screen = psp2d.Screen()
image = psp2d.Image("ICON0.PNG")
screen.blit(image, dx=50, dy=50)
screen.blit(image, dx=200, dy=150, dw=32, blend=True)
screen.swap()
while True:
    pad = psp2d.Controller()
    if pad.circle:
        break
```

---

### Working with Colors

The function `psp2d.Color` is used to create color palettes based on an RGB basis:

```python
psp2d.Color(R, G, B)
```

To make the red color:

```python
psp2d.Color(255, 0, 0)
```

0 is minimum and 255 maximum. The same applies to green and blue:

```python
psp2d.Color(0, 255, 0)  # Green
psp2d.Color(0, 0, 255)  # Blue
```

Other colors through trial and error:

```python
WHITE_COLOR  = psp2d.Color(255, 255, 255)
CLEAR_COLOR  = psp2d.Color(0, 0, 0)
RED_COLOR    = psp2d.Color(255, 0, 0)
GREEN_COLOR  = psp2d.Color(0, 255, 0)
BLUE_COLOR   = psp2d.Color(0, 50, 255)
YELLOW_COLOR = psp2d.Color(255, 255, 0)
```

`CLEAR_COLOR` is just black. Play around with the different numbers to get different colors.

---

### Pixel Fixer Example (from psptools)

```python
def pixel_fix():
    # Stuck pixel fixer
    x12 = True
    log.write("pixel fixer started\n")
    while x12 == True:
        pad = psp2d.Controller()
        time.sleep(0.07)
        img.clear(WHITE_COLOR)
        scr.blit(img)
        scr.swap()
        time.sleep(0.07)
        img.clear(RED_COLOR)
        scr.blit(img)
        scr.swap()
        time.sleep(0.07)
        img.clear(GREEN_COLOR)
        scr.blit(img)
        scr.swap()
        time.sleep(0.07)
        img.clear(BLUE_COLOR)
        scr.blit(img)
        scr.swap()
        time.sleep(0.07)
        img.clear(YELLOW_COLOR)
        scr.blit(img)
        scr.swap()
        if pad.triangle:
            x12 = False
            log.write("pixel fixer ended by user\n")
            main()
```

There is no much mystery here — basically the same code gets repeated over and over until the user presses triangle.

**Main logic:**

```python
time.sleep(0.07)  # Delay between screen change
img.clear(COLOR)  # The color to fill the screen with
scr.blit(img)     # Draw image to screen
scr.swap()        # Swap buffers
```

---

**Previous:** [viewtopic.php?f=5&t=13373](http://wololo.net/talk/viewtopic.php?f=5&t=13373)  
**Next:** [viewtopic.php?f=37&t=33157](http://wololo.net/talk/viewtopic.php?f=37&t=33157)

---
