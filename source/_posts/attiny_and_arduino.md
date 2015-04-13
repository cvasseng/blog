title: ATTiny25 and Arduino
categories:
- electronics
- arduino
tags:
- attiny
- PCB
---

I've had a tube of ATTiny25's laying around for a while, and finally decided to to something more or less useful with them. I found this [great ATtiny arduino core](https://code.google.com/p/arduino-tiny/), and whipped up a quick schematic and board layout to make it easier to prototype with, and sent it off to [OSH Park](http://oshpark.com). Three boards cost me less than $4 ($3.40-ish), including shipping. Crazy good value.

![Schematic](images/tiny.png)

A couple of weeks later, this appeared in the mail:
![Boards](images/attiny_board.jpg)

Which I quickly assembled and ran the Arduino blink program on after burning the above bootloader to it. I'm using the [Sparkfun AVR Pocket Programmer](https://www.sparkfun.com/products/9825) which works really well. It took me about 20min from opening the purple envelope with the boards until I had it all running on a breadboard.

![Blink](images/attiny_blink.jpg)

Note that it's not really that convinent to run Arduino on the 25 chip as it only has 2kb of flash memory. This means that you can pretty much forget about including any libraries. It's much better suited to programming in either assembly or c, but this seemed to be the shortest path to verifying that my boards worked. Which they did. :)

I'm pretty pleased with the boards - even though they have some potential issues with 90 degree traces. The only problem is that they are.. well, tiny. Really tiny. The silkscreen is unreadable without a magnifying glass, at least the pin out part of it.

[Eagle schematics](files/tiny.sch)
[Eagle PCB layout](files/tiny.brd)
