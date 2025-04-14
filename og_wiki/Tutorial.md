> **Disclaimer:** 
>
>This page was archived from http://python-psp.net/trac/wiki/Tutorial
>
> It may not fully reflect the most recent build of the PSP Python port.

# Introduction 

This tutorial is intended for people who have already a knowledge of Python. If you're new to Python, take a look at the excellent [Python tutorial](http://docs.python.org/tut/tut.html). I have found that reading the [Library reference](http://docs.python.org/lib/lib.html ) in full is an enlightening experiment (don't try to remember it all! But after reading it you'll find you know exactly where to look for when a problem arises). Another interesting document to read once you've grasped the basics of the language is the [PEP 08](http://www.python.org/peps/pep-0008.html), containing useful style guides.

This tutorial will try to step through all the classes exposed by the psp2d module, but will obviously not cover them in depth. For a complete reference, see the [wiki:PSP2d module reference].

## Hello world

As the tradition goes, we'll start by printing 'Hello, world' to the screen. Make sure you have correctly [wiki:InstallationGuide installed] Python on your PSP, and save the following code to the file 'script.py' in the same directory as EBOOT.PBP. You will also need to put the following font file in this directory:

```
#!html
<a href="http://python-psp.net/trac/chrome/site/doc/font.png">download font file</a>
```

```
#!python
#-*- coding: ISO-8859-1 -*-

import psp2d

def main():
    scr = psp2d.Screen()
    fnt = psp2d.Font('font.png')
    img = psp2d.Image(480, 272)
    img.clear(psp2d.Color(0, 0, 0))
    fnt.drawText(img, 0, 0, 'Hello, world')
    scr.blit(img)
    scr.swap()
    while True:
        pad = psp2d.Controller()
        if pad.circle:
            break

if __name__ == '__main__':
    try:
        main()
    except:
        import traceback
        traceback.print_exc(file = file('trace.txt', 'w'))
```

![shot01](img/shot01.png)

You can exit the program by pressing Circle. Let's first evacuate the try/except construct; this is only here to dump the call stack if an exception occurs. Useful for debugging. Let's walk through the main function.

```
#!python
    scr = psp2d.Screen()
```

The Screen class represents the physical screen of the PSP, you'll need it to display anything. Note that every instance of this class actually represent the same physical screen (it's actually a Singleton, or rather a Borg). With a few differences, the Screen class has the same methods as the Image class, so you can use it almost like you would use any other image.

```
#!python
    fnt = psp2d.Font('font.png')
```

This instantiates a Font  object, which is needed whenever you want to draw text. There is no built-in font; you need an SFont-like PNG file.

```
#!python
    img = psp2d.Image(480, 272)
```

Here, we create an image with the same dimensions as the PSP screen. We'll use this image to draw the text. In earlier versions, you could only draw text to an image, and not to the screen. I kept the old version of the script for educational purposes; nowadays you should directly draw the text to the screen. It's '''way''' faster.

```
#!python
    img.clear(psp2d.Color(0, 0, 0))
```

We clear the image using a black color. Colors are abstracted through the Color class, which is instantiated with three integer arguments between 0 and 255: red, green and blue values. There is also a fourth, optional argument that specifies the level of transparency; it defaults to 0 (opaque).

```
#!python
    fnt.drawText(img, 0, 0, 'Hello, world')
```

Actually draws the text at coordinates (0, 0) in the image. You can pass a Screen instance as first argument as well.

```
#!python
    scr.blit(img)
```

This is the simplest use of the 'blit' method. This method is common to Screen and Image objects; it copies the argument to the instance. The complete signature is

```
#!python
obj1.blit(obj2, sx, sy, w, h, dx, dy, blend)
```

All arguments except the first are optionnal; the effect is to copy the rectangle (sx, sy, sx+w, sy+h) from obj2 to obj1 at coordinates (dx, dy). The 'blend' argument is a Boolean which defaults to False. If it is True, the transparency level of the source object (obj2) is taken into account when blitting.

```
#!python
    scr.swap()
```

Drawing to the screen uses double buffering. That means that instead of having one buffer to represent the screen, there are two, the display buffer and the draw buffer. All drawing operations are directed to the draw buffer, which is not actually displayed. The 'swap' method of the Screen object swaps the draw buffer and the display buffer, so that what we just drawed is actually displayed. This is necessary to avoid flicker; to learn more about double buffering, see the [article](http://en.wikipedia.org/wiki/Double_Buffering) on Wikipedia.

```
#!python
    while True:
        pad = psp2d.Controller()
        if pad.circle:
            break
```

This loops until the Circle button is pressed. The Controller class reads the state of the pad upon instantiation and makes it available through read-only properties.

## Dancing hello world

Now we'll try to add some animation to that, and a background image. Copy the following image as "background.png" to your memory stick (same directory as font.png):

[[Image(htdocs:doc/background.png)]]

We'll center the 'hello, world' message and make each letter move vertically on a sinusoid. Here, we must take into account the fact that SFonts are variable-width.

```
#!python
#-*- coding: ISO-8859-1 -*-

import psp2d, math

def main(msg):
    screen = psp2d.Screen()
    bgimg = psp2d.Image('background.png')
    font = psp2d.Font('font.png')

    widths = []
    totalw = 0
    for c in msg:
        widths.append(font.textWidth(c))
        totalw += font.textWidth(c)

    y = (272 - font.textHeight(msg)) / 2
    x = (480 - totalw) / 2

    angle = 0.0

    while True:
        img = psp2d.Image(bgimg)
        dx = 0
        for idx, c in enumerate(msg):
            font.drawText(img, x + dx, y + 20 * math.sin(angle + 0.2 * idx), c)
            dx += widths[idx]

        screen.blit(img)
        screen.swap()
        angle += 0.2

        pad = psp2d.Controller()
        if pad.circle:
            break

if __name__ == '__main__':
    try:
        main('Hello, world!')
    except KeyboardInterrupt:
        pass
    except:
        import traceback
        traceback.print_exc(file = file('trace.txt', 'w'))
```

[[Image(htdocs:doc/shot02.png)]]

I won't spend time on the 'algorithmic' side, let's just see what's new here.

```
#!python
    bgimg = psp2d.Image('background.png')
```

Instead of creating an empty image, you can of course load a file. PNG files as well as JPEG files are supported. The dimensions of the image are accessible through the read-only properties 'width' and 'height'.

```
#!python
        widths.append(font.textWidth(c))
```

As SFonts are variable-width, you need to take each character into account. The Font.textWidth method returns the width, in pixels, necessary to render the specified text with this font. The Font.textHeight  method does the same for the height, but actually always returns the same value for a given font. you still need to pass it a string though.

```
#!python
        img = psp2d.Image(bgimg)
```

This is the third and last way to create an image; if you pass another image to the constructor, it will copy it.

## Optimizing

Since 2005-11-12, a new underlying implementation allows one to draw text directly to the screen. Let's modify the script:

```
#!python
#-*- coding: ISO-8859-1 -*-

import psp2d, math

def main(msg):
    screen = psp2d.Screen()
    bgimg = psp2d.Image('background.png')
    font = psp2d.Font('font.png')

    widths = []
    totalw = 0
    for c in msg:
        widths.append(font.textWidth(c))
        totalw += font.textWidth(c)

    y = (272 - font.textHeight(msg)) / 2
    x = (480 - totalw) / 2

    angle = 0.0

    while True:
        # img = psp2d.Image(bgimg) ## REMOVED
	screen.blit(bgimg) # ADDED
        dx = 0
        for idx, c in enumerate(msg):
            # font.drawText(img, x + dx, y + 20 * math.sin(angle + 0.2 * idx), c) ## REMOVED
            font.drawText(screen, x + dx, y + 20 * math.sin(angle + 0.2 * idx), c) # ADDED
            dx += widths[idx]

        # screen.blit(img) ## REMOVED
        screen.swap()
        angle += 0.2

        pad = psp2d.Controller()
        if pad.circle:
            break

if __name__ == '__main__':
    try:
        main('Hello, world!')
    except KeyboardInterrupt:
        pass
    except:
        import traceback
        traceback.print_exc(file = file('trace.txt', 'w'))
```

Try both the old and the new version, and you'll see why it's better to blit() or drawText() directly to the screen instead of using an intermediate buffer.

## Adding music

We'll use the music distributed with [Snake](http://www.1homebrew.com/psp/snake.shtml), stranglehold.xm. Change the import line to

```
#!python
import psp2d, pspsnd, math
```

And add the following code at the start of the main() function:

```
#!python
    pspsnd.setMusicVolume(128)
    music = pspsnd.Music('stranglehold.xm')
    music.start()
```

Don't forget to copy stranglehold.xm to the PSP. Done! See the pspsnd [wiki:PSPSnd module reference] for details.

## Adding interactivity

Now we'll code a simple menu allowing the user to change the animation settings. The active menu item will be drawn on a blue rectangle with an alpha value of 200; other items on a blue rectangle with alpha value 100. Let's first define the interface of the menu items; this is not necessary but it helps, as documentation:

```
#!python
class MenuItem:
    """Abstract base class for menu items"""
    def __init__(self, name):
        """Each menu item has a name. It may be used as a label."""
        self.name = name

    def getDimension(self, font):
        """This method should return a 2-tuple (w, h) holding the preferred
        width and height for this menu item."""
        raise NotImplementedError

    def draw(self, font, drw, x, y, w, h):
        """Override this to do the actual drawing. You should draw on the 'drw'
        drawable, int the (x, y, w, h) rectangle, using the 'font' font."""
        raise NotImplementedError

    def onLeft(self):
        """Will be called if the user presses the Left button when this menu
        item is active"""
        pass

    def onRight(self):
        """Same as onLeft for the Right button"""
        pass
```

This code goes either before the main function of immediately after it. The methods defined here will be called when appropriate by the Menu class.

Now let's go directly for the Menu class to see if we haven't forgotten something in the interface.

```
#!python
class Menu:
    def __init__(self, x, y):
        """The menu will be displayed at coordinates (x, y). Initially empty.
        Default selection is item #0."""
        self.x = x
        self.y = y

        self.w = 0
        self.h = 0

        self.items = []

        self.abg = None
        self.bg = None

        self.current = 0

    def addItem(self, item):
        """Add an item to this menu."""
        self.items.append(item)

    def realize(self, font):
        """You should call this when you're finished adding items to the menu."""

        self.w = 0
        self.h = 0

        for item in self.items:
            w, h = item.getDimension(font)
            w += 20
            h += 10

            self.w = max(self.w, w)
            self.h = max(self.h, h)

        self.abg = psp2d.Image(self.w, self.h)
        self.abg.clear(psp2d.Color(0, 0, 255, 200))

        self.bg = psp2d.Image(self.w, self.h)
        self.bg.clear(psp2d.Color(0, 0, 255, 100))

    def draw(self, font, drw):
        cy = self.y
        for idx, item in enumerate(self.items):
            if idx == self.current:
                bg = self.abg
            else:
                bg = self.bg

            drw.blit(bg, dx = self.x, dy = cy, blend = True)
            item.draw(font, drw, self.x, cy, self.w, self.h)
            cy += self.h

    def update(self, pad):
        """The main loop should call this method to allow the menu to
        react to user actions. 'pad' should be a Controller instance."""

        if pad.left:
            self.items[self.current].onLeft()

        if pad.right:
            self.items[self.current].onRight()

        if pad.up:
            self.current = (self.current - 1 + len(self.items)) % len(self.items)

        if pad.down:
            self.current = (self.current + 1) % len(self.items)
```

The 'realize' method computes an 'ideal' size for the whole menu, based on the sizes of the menu items. It then creates two blue images to be used as background for the menu items.

The values that the user can modify are, first the amplitude (the 20 factor), the 'slope' (the 0.2 factor)

```
#!python
            font.drawText(img, x + dx, y + 20 * math.sin(angle + 0.2 * idx), c)
```

and finally the angle step:

```
#!python
        angle += 0.2
```

As these are all float values, we'll write a !FloatMenuItem class, which holds a float value and allows to increment or decrement it by a fixed amount:

```
#!python
class FloatMenuItem(MenuItem):
    def __init__(self, value, step, name):
        MenuItem.__init__(self, name)

        self.value = value
        self.step = step

    def getDimension(self, font):
        txt = '%s: %.2f' % (self.name, self.value)
        return (font.textWidth(txt), font.textHeight(txt))

    def draw(self, font, drw, x, y, w, h):
        txt = '%s: %.2f' % (self.name, self.value)
        font.drawText(drw,
                      x + (w - font.textWidth(txt))/2,
                      y + (h - font.textHeight(txt))/2,
                      txt)

    def onLeft(self):
        self.value -= self.step

    def onRight(self):
        self.value += self.step
```

All we have to do now is instantiate the menu and items, and replace the hardcoded values. Here is the whole script.

```
#!python
#-*- coding: ISO-8859-1 -*-

import psp2d, pspsnd, math

def main(msg):
    screen = psp2d.Screen()
    bgimg = psp2d.Image('background.png')
    font = psp2d.Font('font.png')

    pspsnd.setMusicVolume(128)
    music = pspsnd.Music('stranglehold.xm', loop = True)
    music.start()

    widths = []
    totalw = 0
    for c in msg:
        widths.append(font.textWidth(c))
        totalw += font.textWidth(c)

    y = (272 - font.textHeight(msg)) / 2
    x = (480 - totalw) / 2

    angle = 0.0

    menu = Menu(10, 10)

    amplitude = FloatMenuItem(20, 0.5, 'Amplitude')
    step = FloatMenuItem(0.2, 0.02, 'Step')
    slope = FloatMenuItem(0.2, 0.02, 'Slope')

    menu.addItem(amplitude)
    menu.addItem(step)
    menu.addItem(slope)

    menu.realize(font)

    while True:
	screen.blit(bgimg)
        menu.draw(font, screen)

        dx = 0
        for idx, c in enumerate(msg):
            font.drawText(screen, x + dx, y + amplitude.value * math.sin(angle + slope.value * idx), c)
            dx += widths[idx]

        screen.swap()
        angle += step.value

        pad = psp2d.Controller()
        if pad.circle:
            break

        menu.update(pad)

class MenuItem:
    """Abstract base class for menu items"""
    def __init__(self, name):
        """Each menu item has a name. It may be used as a label."""
        self.name = name

    def getDimension(self, font):
        """This method should return a 2-tuple (w, h) holding the preferred
        width and height for this menu item."""
        raise NotImplementedError

    def draw(self, font, drw, x, y, w, h):
        """Override this to do the actual drawing. You should draw on the 'drw'
        drawable, int the (x, y, w, h) rectangle, using the 'font' font."""
        raise NotImplementedError

    def onLeft(self):
        """Will be called if the user presses the Left button when this menu
        item is active"""
        pass

    def onRight(self):
        """Same as onLeft for the Right button"""
        pass

class Menu:
    def __init__(self, x, y):
        """The menu will be displayed at coordinates (x, y). Initially empty.
        Default selection is item #0."""
        self.x = x
        self.y = y

        self.w = 0
        self.h = 0

        self.items = []

        self.abg = None
        self.bg = None

        self.current = 0

    def addItem(self, item):
        """Add an item to this menu."""
        self.items.append(item)

    def realize(self, font):
        """You should call this when you're finished adding items to the menu."""

        self.w = 0
        self.h = 0

        for item in self.items:
            w, h = item.getDimension(font)
            w += 20
            h += 10

            self.w = max(self.w, w)
            self.h = max(self.h, h)

        self.abg = psp2d.Image(self.w, self.h)
        self.abg.clear(psp2d.Color(0, 0, 255, 200))

        self.bg = psp2d.Image(self.w, self.h)
        self.bg.clear(psp2d.Color(0, 0, 255, 100))

    def draw(self, font, drw):
        cy = self.y
        for idx, item in enumerate(self.items):
            if idx == self.current:
                bg = self.abg
            else:
                bg = self.bg

            drw.blit(bg, dx = self.x, dy = cy, blend = True)
            item.draw(font, drw, self.x, cy, self.w, self.h)
            cy += self.h

    def update(self, pad):
        """The main loop should call this method to allow the menu to
        react to user actions. 'pad' should be a Controller instance."""

        if pad.left:
            self.items[self.current].onLeft()

        if pad.right:
            self.items[self.current].onRight()

        if pad.up:
            self.current = (self.current - 1 + len(self.items)) % len(self.items)

        if pad.down:
            self.current = (self.current + 1) % len(self.items)

class FloatMenuItem(MenuItem):
    def __init__(self, value, step, name):
        MenuItem.__init__(self, name)

        self.value = value
        self.step = step

    def getDimension(self, font):
        txt = '%s: %.2f' % (self.name, self.value)
        return (font.textWidth(txt), font.textHeight(txt))

    def draw(self, font, drw, x, y, w, h):
        txt = '%s: %.2f' % (self.name, self.value)
        font.drawText(drw,
                      x + (w - font.textWidth(txt))/2,
                      y + (h - font.textHeight(txt))/2,
                      txt)

    def onLeft(self):
        self.value -= self.step

    def onRight(self):
        self.value += self.step

if __name__ == '__main__':
    try:
        main('Hello, world!')
    except KeyboardInterrupt:
        pass
    except:
        import traceback
        traceback.print_exc(file = file('trace.txt', 'w'))
```

Trying this script, you may notice a problem: as the pad is read frequently, pressing the Right button, for instance, may invoke the onRight method several times. This is even more annoying with Up and Down, as it becomes difficult to navigate in the menu. We can work around that by only taking the buttons into account if the pad state has changed.

The Controller class implements a comparison operator for that. It doesn't take the analog pad into account. So let's add the following code to `Menu.__init__`:

```
#!python
        self.old = None
```

and this to the start of Menu.update:

```
#!python
        if self.old == pad:
            return
        self.old = pad
```

And we're done.

[[Image(htdocs:doc/shot03.png)]]
