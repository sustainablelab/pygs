`pygs` is a Python package to simplify developing with pygame.

Pronounced "pigs", short for PYGame Stuff.

# Why `pygs`
`pygame` is a low-level tool for interactive applications.
Low-level is great, but it means lots of boiler-plate code.
`pygs` makes pygame applications a little bit higher-level.

I did not set out to write a pygame wrapper. `pygs` is
where my pygame code goes when I get tired of repeating it.

I started `pygs` to aid in hobby projects where it lived happily
in my `USER_SITE`. Then I started using `pygs` for work and I
needed a way to share it with customers.

I am **very actively** adding to `pygs`. Check for changes:

- if cloned locally, do `git remote update`
- or if installed, update to the latest with `pip install --upgrade pygs`

# Platform compatibility
I'm using Python3.8 and `pygame 1.9.6`. I've only tested on
Windows, but everything in `pygs` should be cross-platform. I
plan to test on Linux Mint.

# Installation

## pip install
Install the `wheel` (PyPI binary) with `pip`:

```bash
$ pip install pygs
```

## or clone repo in `USER_SITE`
**clone this repository in `USER_SITE`.** This makes the
package visible to Python *without needing to edit `PYTHONPATH`*.

`USER_SITE` is different for every Python installation.

Find the `USER_SITE` path with:

```bash
$ python -m site --user-site
```

(Create the `USER_SITE` path if it does not exist.)

## or clone repo anywhere and run setup.py develop
**install from your repository clone** by installing
`setuptools`:

```bash
$ pip install setuptools
```

And running the `setup.py` script with the `develop` command:

```bash
$ python script.py develop
```

This is like `pip install`, but it places a symbolic link in the
Python install folder instead of placing the actual project files
in the Python install folder.

Use this installation method if you plan to modify `pygs`. This
way any changes to your local repository are picked up without
having to reinstall `pygs`.

# Quick example script using `pygs`

This example opens a window and draws a line. Quit by pressing
`q` or by clicking on the usual Window close button to quit.

```python
#!python.exe
# -*- coding: utf-8 -*-
'''Draw a line with pygame.'''
import pygame
import pygs

if __name__ == '__main__':
    rgb = pygs.RGB()
    win = pygs.Window()
    win.open_window(1200,600)
    print(f"Display window size: {win.width}x{win.height}")
    clock = pygs.Clock(framerate=50)

    '''game loop'''
    quit = False
    while not quit:
        clock.tick()

        '''--- EVENTS ---'''
        for event in pygame.event.get():
            kp = pygame.key.get_pressed()
            km = pygame.key.get_mods()
            quit = pygs.user.quit(event, kp, km)

        '''--- UPDATE SCREEN ---'''
        win.surface.fill(rgb.blackestgravel)
        pygame.draw.aalines(
            win.surface,
            rgb.dress,
            False, # if True, connect first and last points
            [(100,100), (200,200), (200,300), (500,300)]
            )

        # Flip to new surface drawing
        pygame.display.flip()
```

# Libraries

## Class Window

Wraps `pygame.display.set_mode()`, pulling several pygame calls
into two methods: `__init__` and `open`.

The application only makes one instance of Window. This Window
singleton holds:

- the top-level **`pygame` surface** (for drawing everything on)
- the *width* and *height* of the main game window

### Using Window

Instantiate a Window:

```python
win = pygs.Window()
```

- initializes pygame
- sets window properties:
    - location
    - caption
    - icon

Open the Window:

```python
win.open()
```

- sets the window size `win.width` and `win.height`
- opens the main game window `win.surface` for drawing to
- the window closes when the application ends
    - this is just pygame's out-of-the-box behavior for closing
      the game window

## Class Clock

This is a very simple wrapper around `pygame.time.Clock()`. All
it does is avoid repeating the framerate value.

Set the framerate when Clock() is instantiated:

```python
clock = pygs.Clock(framerate=60)
```

Then in the game loop, tick the `clock`:

```python
clock.tick()
```

## colors: Namedtuple RGB and HEX
Color is a distraction when I first start development on
an application. I want nice contrast that is also easy on my eyes
and is good enough if I never bother changing it.

This library gives me Steve Losh's Badwolf scheme in RGB and HEX
format. This is my default scheme for Vim, so it is a natural
default scheme for my applications. It is just what I am used to
looking at. I encourage Vim users to do something similar with
their favorite color schemes.

Pygame uses RGB. Instantiate an `rgb` version of the Badwolf
palette:

```python
rgb = pygs.RGB()
```

Use one of the gravels as a background:

```python
win.surface.fill(rgb.blackestgravel)
```

Draw a tardis-colored plot (reminds me of the C64 blue):

```python
pygame.draw.aalines(
    win.surface,
    rgb.tardis,
    False, # if True, connect first and last points
    meaningful_data # XY plot data [(x0,y0), ... (xn,yn)]
    )
```

The Badwolf scheme is a small set of colors with easy-to-remember
names:

- the gravels:
    - wide range of brownish-greys from almost white to almost
      black
    - good for background fills and background line work of
      varying emphasis: grid lines, box borders, text
    - e.g., if the background is `blackestgravel`, then:
        - a non-interactive text title is just `gravel`
        - interactive text is `darkgravel` to indicate disabled
          or out-of-focus
        - text is `brightgravel` or a highlight color to indicate
          it is in-focus or it it just became active
- highlight colors:
    - tardis, taffy, saltwatertaffy, dalespale, orange, lime,
      dress
    - I usually have two or three of these in an application,
      e.g.:
    - a tardis command line
    - a saltwatertaffy plot

And, of course, there is simple black and white.

Black (0, 0, 0) is `coal`:

```python
rgb.coal
```

White (255, 255, 255) is `snow`:

```python
rgb.snow
```

## user interaction

Pygame user interaction is detected via key-presses and events.
Library `user` is a collection of functions that check for common
user interactions and return a Boolean.

### pygame events, key presses, and key modifications
Typical pygame applications check for user interaction on each
iteration of the game loop:

```python
for event in pygame.event.get():
    kp = pygame.key.get_pressed()
    km = pygame.key.get_mods()
```

This stores user interaction in three variables:

- events
- key presses (like a letter or a number)
- key modifications (like shift or control)

Every function in `pygs.user` takes all three of these variables. I
pass all three to simplify the interface. I don't want to bother
thinking about out which ones to pass.

### quit is a typical example
For example, every application needs a way for the user to quit.
The game loop loops until `quit` is True, and every iteration
checks if the user quit. The value of `quit` is updated by
calling `pygs.user.quit()`:

```python
quit = pygs.user.quit(event, kp, km)
```

In this case, all three variables are actually used:

- clicking the Window's red X is an event
- ctrl-q is a keyboard shortcut requiring `kp` for detecting `q` and `km` for detecting if `ctrl` was held down

### pygame events
Events are the catch-all for everything else:

- some events are defined by pygame, such as JOYBUTTONDOWN and
  JOYAXISMOTION
- events are also defined by the developer, e.g., a text-entry
  object might trigger an event when the user presses enter or
  when the users selects text with the mouse

### goals for the `user` library
There is a lot of boilerplate in detecting specific key-presses
and events.

The `user` library is my attempt to simplify this with a
higher-level view asking "what did the user do?" in the form of
functions that return either True or False.

There are many high-level views. This library just represents the
stuff I find most useful.

I intent to redo this library to enable:

- customization in the application code
- use of user-specific keyboard shortcuts, like a `.rc` file

As of now, the library is a mixture of high-level user
interaction, such as pygs.user.quit(), and low-level interaction
such as checking for specific key presses. I use these when the
interpretation of user-intent is application specific. For
example, this returns True if the user presses capital x:

```python
pygs.user.pressed_X(event, key_pressed, key_mods)
```

## plot

`plot` handles:

- reading data from file
- scaling the data values for fitting the plot on screen

Either the application generates 2d data to plot, or there is a
file with 2d data. Either way, there are x and y values for
plotting.

- x values are stored as a list of floating point values
- y values are stored as a list of floating point values

Right now it's up to the application to flip the data as needed,
dealing with the pygame convention that y=0 is the top line of
the window and y increases moving towards the bottom of the
window.

Like `user`, `plot` is a work in progress. Right now, `plot` is
only handling conversions from the native data to the window
pixel values, scaling data to fit in the plot-window size and
rounding values to integers.

Eventually `plot` will include commands for drawing the plot,
managing multiple plot lines, changing plot styles (colors,
line-thickness, dot-size, line on/off, dot on/off), and typical
user interaction such as changing the plot axes and zooming.

## WIP libraries

### lines

The `pygame` line drawing interfaces specifies line segments with
a start x,y and a stop x,y.

I'm working on a `lines` library that provides a higher-level
interface for working with lines:

- `line`:
    - specify a point (x,y) the line passes through
    - specify the slope

The library converts lines to the visible line segment that fills
the game window.

Building on this `line` interface, `lines` provides a `Tiles`
class.

Instantiate `Tiles` to generate a grid of parallelograms that
tile a portion of the game window. This started out of a need to
draw grids for axonometric (isometric) projection. With the right
parameter values, this simplifies to a rectangular grid for
plotting 2d data.
