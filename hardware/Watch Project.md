# Intro
As my first project for the Pico, I chose to do a smart watch.
# Research
## General
[Pimoroni Pico Lipo](https://shop.pimoroni.com/products/pimoroni-pico-lipo?variant=39386149093459) that I'm planning to use for this project has their own [firmware](https://shop.pimoroni.com/products/pimoroni-pico-lipo?variant=39335427080275).
## Bitmap Fonts

- Lipo firmware has two related posts: one is regarding [vector fonts](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.5), the [other](https://github.com/pimoroni/pimoroni-pico/releases/tag/v1.20.3) has to do with their custom [font drawing utility](https://github.com/gadgetoid/pgfutil) (which is of little use to me).
- Someone did a great 8x4 [bitmap font called Miniwi](https://github.com/a780201/miniwi). 
- There's a [great website](https://int10h.org) that hosts many of authentic retro fonts, including [some](https://int10h.org/oldschool-pc-fonts/fontlist/font?ibm_bios#-) with Cyrillic support.

## Data over audio
- [Pico Lipo](https://shop.pimoroni.com/products/pimoroni-pico-lipo?variant=39386149093459) doesn't have any wireless connectivity, so it's still a question of how to sync it with anything that knows anything about, say, the weather. I would love to do this in a modem-like fashion over audio, but so far I was only able to find [[Python#Transferring data over audio|Python libraries]] that work to that end—and not Micropython.