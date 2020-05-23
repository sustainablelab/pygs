`pygs` is a Python package to simplify developing with pygame.

Pronounce "pygs" as "pigs". It is short for PYGame Stuff.

# Why did I make `pygs`
I recently discovered `pygame` is an amazing tool for writing
interactive applications. I got tired of remembering `pygame`
boiler-plate code and started `pygs` to aid in hobby projects.
More recently, I'm using this at work too.

I am **very actively** adding to `pygs`. Check for changes with
`git remote update`.

# Setup
I'm using Python3.8 and `pygame 1.9.6`. I've only tested on
Windows, but everything in `pygs` should be cross-platform. I
plan to test on Linux Mint.

# Installation
I'll get around to figuring out how to share packages on PyPi.

For now, I recommend you **clone this repository in
`USER_SITE`.** This makes the package visible to Python *without
needing to edit `PYTHONPATH`*.

`USER_SITE` is different for every Python installation.

Find the `USER_SITE` path with:

```bash
$ python -m site --user-site
```

(Create the `USER_SITE` path if it does not exist.)

# Quick example

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
