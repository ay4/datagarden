> [!INFO] Also see
> [[Micropython]], [[Microelectronics I have]]
# Quick Links

-  [Pico Graphics Docs](https://github.com/pimoroni/pimoroni-pico/tree/main/micropython/modules/picographics)
- [Pico GFX Examples](https://github.com/pimoroni/pimoroni-pico/tree/main/micropython/examples/gfx_pack)
- [GFX Pack Docs](https://github.com/pimoroni/pimoroni-pico/blob/main/micropython/modules_py/gfx_pack.md)
# Research
## General

[Pimoroni Pico Lipo](https://shop.pimoroni.com/products/pimoroni-pico-lipo?variant=39386149093459) that I'm planning to use for this project has their own [firmware](https://github.com/pimoroni/pimoroni-pico/releases).
## Bitmap Fonts

- Lipo firmware has two related posts: one is regarding [vector fonts](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.5), the [other](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.3) has to do with their custom [font drawing utility](https://github.com/gadgetoid/pgfutil) (which is of little use to me).
- Someone did a great 8x4 [bitmap font called Miniwi](https://github.com/a780201/miniwi). 
- There's a [great website](https://int10h.org) that hosts many of authentic retro fonts, including [some](https://int10h.org/oldschool-pc-fonts/fontlist/font?ibm_bios#-) with Cyrillic support.

## Data over audio

- [Pico Lipo](https://shop.pimoroni.com/products/pimoroni-pico-lipo?variant=39386149093459) doesn't have any wireless connectivity, so it's still a question of how to sync it with anything that knows anything about, say, the weather. I would love to do this in a modem-like fashion over audio, but so far I was only able to find [[Python#Transferring data over audio|Python libraries]] that work to that end—and not Micropython.

# Experiments
## Vector fonts

Vector fonts do work! Here's the purest example notably dependent on using a non-RGB screen and the font file in the [all-right format](https://github.com/lowfatcode/alright-fonts) available in the root of Pi. They're also pretty quick.

```python
from gfx_pack import GfxPack
from picovector import PicoVector, ANTIALIAS_NONE, ANTIALIAS_X4, ANTIALIAS_X16

gp = GfxPack()
display = gp.display
WIDTH, HEIGHT = display.get_bounds()


vector = PicoVector(display)
vector.set_antialiasing(ANTIALIAS_X4)

#
# Setting the font
# vector.setfont("path-to-font", size)
#
result = vector.set_font("/IndieFlower-Regular.af", 20) 

#
# Sets up a handy function we can call to clear the screen
#
def clear():
    display.set_pen(0)
    display.clear()
    display.set_pen(15)

#
# gp.set_backlight(RED, GREEN, BLUE, WHITE)
#

gp.set_backlight(0, 0, 0, 255)
clear()

#
# Output
# vector.text("TextHere",X,Y,ANGLEDEG)
#
vector.text("Hello world", 0, 0, 0)


#
# Update screen
#
display.update()

```

## Custom fonts

After
```sh
pip3 install freetype-py
pip3 install simplification
```

I was able to simply clone the [alright-fonts repository](https://github.com/lowfatcode/alright-fonts) and create a custom af font by running something like

```
./afinate --font ~/Desktop/IBMPlexMono-Regular.ttf --characters='abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZабвгдеёжзийклмнопрстуфхцчшщъыьэюя0123456789' ~/Desktop/IBMALL.af
```


## Cyrillic (and box art) support
It even showed me that Cyrillic characters are being properly converted! However, I wasn't able to output any of them. Also, it doesn't allow to change the character table. The bitmap fonts are also not an option as they are custom.

So, Pimoroni **don't support Unicode** and I will have to do everything with spritesheets I guess. With this in mind, PNG [sprite sheets are explained here](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.4).

![[Amstrad_PC_spritesheet.png]]

I made an Amstrad PC sprite sheet by hand in Pixelmator using my own character order. This font is [represented atint10h](https://int10h.org/oldschool-pc-fonts/fontlist/font?amstrad_pc). It's a 8x8 font that is supposed to be universal. Besides other things, it can be used for box graphics.  The others might be shorter with just the characters, numbers and the : sign (for clocks). 

I should probably continue with [IBM VGA 8x16](https://int10h.org/oldschool-pc-fonts/fontlist/font?ibm_vga_8x16#-) (**✓ DONE**) for a larger font within the same widths. Some interest is there for [ToshibaSat 8x16](https://int10h.org/oldschool-pc-fonts/fontlist/font?toshibasat_8x16#-) Также [Rainbow 100](https://int10h.org/oldschool-pc-fonts/fontlist/font?rainbow100_re_80#-) представляет интерес из-за матрицы 10x10.

Look for everything at `/Users/neiaglov/hardware/yesno/fonts`

## Icons

In the future, it might make sense to also look at the following icon sets:
- [Fontawesome](https://fontawesome.com/icons)
- [Phosphor](https://phosphoricons.com)
- [Framework 7](https://framework7.io/icons/)
- [Icofont](https://icofont.com)
- [Bootstrap Icons](https://icons.getbootstrap.com)

However they all work best with hinting which I don't have.


## First PNG test

I was able to successfully show an (uploaded) PNG file on Pico called test.png. It has transparent background and **white** symbols that show as black symbols on my monochrome display. Here's the code:

``` python
from gfx_pack import GfxPack
from pngdec import PNG

gp = GfxPack()
gp.set_backlight(0, 0, 0, 255)
display = gp.display
WIDTH, HEIGHT = display.get_bounds()

display.clear()
    
png = PNG(display)
png.open_file("test.png")
png.decode(0, 0)
display.update()
```

**Importantly,** on the monochrome display it seems that 'black' is background, while 'white' shows up as black.
## Inverse alpha PNG work!!

![[inverse_PNG_demo.jpg]]

Now I took a PNG done in black letters (white in the screen's understanding). I first filled all the screen with white color (black in the screen's understanding) and output a PNG. This resulted in the correct transparency. The code:

``` python
from gfx_pack import GfxPack
from pngdec import PNG

gp = GfxPack()
gp.set_backlight(0, 0, 0, 255)
display = gp.display
WIDTH, HEIGHT = display.get_bounds()

def clear():
    display.set_pen(15)
    display.clear()
    display.set_pen(0)


clear()    
png = PNG(display)
png.open_file("test2.png")
png.decode(0, 0)
display.update()
```
# Architectural Notes
## Dealing with Sprite Fonts

### ayTextStyle Class

Passed a font folder, scale, something else?..

``` python
IBM_large=ayTextStyle("/fonts/ibm_8x16", 1, screen_width, screen_height)
```

When an object is created in this way, `init` calls an internal function, `loadfont("/fonts/ibm_8x16")`, which:
- Checks the font file and font config are in place
- Tries to read the config and see if the sprite sheet is needed size and all
- Forms the class variable, `font_matrix` which is a dictionary so that `font_matrix['a']=(5,4,7,8)` where `'a'` is the character and `(5,4,7,8)` are its coordinates, width and height for the pngdecode to print. Quick ref: `png.decode(0, 0, source=(32, 48, 24, 16), scale=(4, 4), rotate=0) # The source argument is the region, in pixels, you want to show from the PNG- offset left and top, plus width and height.` From [here](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.4).

#### No-problem printing: pr_pr
ayTextStyle should provide nice functions. `pretty_print` or `pr_pr` should simply print the text at the same position (and with the same parameters) it started from last time:

``` python
IBM_large.pr_pr("Hello there")
```

- For this, an variables `print_start_x, print_start_y, print_break, print_alignment, print_rotation ` should be set
- Should probably return something nice, like a tuple with coordinates of the last letter (for convenience, `x` should be the letter's end and `y` — the letters top, so that other printing functions could take it from there).

#### Yes-problem printing: long_pr
`long_pr` is inspired by the printing function of Pimoroni:

``` python
IBM_large.long_pr(text, start_x, start_y, break, align, rotation)
```

where:
- `start_x` (optional, defaults to `0`) — coordinate to start writing text
- `start_y` (optional, defaults to `0`) — coordinate to start writing text
- `break` (optional, defaults to screen width/height): amount of pixels after which start wrapping
- `align` (optional, defaults to `left`): `left`, `right` or `justify`
- `rotation`: (optional defaults to 0): `0`, `90`, `180` or `270`

I suppose I must start with `start_x`, `start_y` and `break` and then add all the other stuff.

Notably, this function should set the internal class variables `print_start_x, print_start_y, print_break, print_alignment, print_rotation ` so that `pr_pr` could use 'em later.

#### Independent style setting
The class should probably give independent access to variables for convenience, so that such things are possible:

``` python
IBM_large.justify=right
pr_pr("Hello there") # Justifies right
```

### ayErrorLog and ayDebugLog

``` python
el=ayErrorLog(screen_width, screen_height)
dl=ayDebugLog(screen_width, screen_height)
```

Then the following must be possible:

```
el.error("Your screen is too soft")
```

The class should decide by itself what to do with that with the default behaviour being clear the screen, write the error and set the class variable that the main loop will check to terminate the program:

``` python
if el.terminal:
	break
```

As for debug, the following must be possible:

``` python
dl.log("Starting the couroutine")
```

The `log` function should be clever enough to:
- Add the name of the function it was called from
- Decide by itself (based on class variables) if it should print something to the console or to the real file log or not print at all.