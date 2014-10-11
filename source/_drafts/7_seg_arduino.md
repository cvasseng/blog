title: 7 segment LEDs with Arduino and shift registers
categories: 
- electronics
- arduino
tags: 
- 74HC595 
- Shift Register
- 7 Segment Display
----
*If you just want to know how to connect everything, feel free to skip the first couple of sections*

### Introduction
You've probably seen those glowing digit displays before - you know; the ones used in digital clock radios and old VCRs. They consist of 7 different "segments" that can be illuminated independently of each other (hence the name) to display numbers from 0 to 9, a few letters (a, b, c, d, e, f, h, p, l), and certain punctuation (- .). They're also pretty cheap and easy to work with, making them good candidates for inclusion in various electronic projects. They do have a few drawbacks though - mainly that they require more current than LCDs, and that they require 8 pins to control (10 for dual digits. Usually.). Output pins on a microcontroller are usually scarce, which brings us to the point of this little post - how to control one using a shift register so that we can do it with 3 pins instead. But first, let's go through our building blocks.

![7 Segment Display](/images/7seg.png)

### 7 Segment Displays - The Basics
So, let's go into a bit more detail on 7 segment displays. Each of the segments contained in the display has a designated input pin used to activate it. The name of the different segments are shown in the below image. By lighting up one or more pins at the same time we can display numbers, a few letters, and some symbols.

![7 Segment - Segments](/images/7seg_letters.png)

There are two different types of 7 segment displays (not counting color options, intensity and so on) - common anode, and common cathode. The difference between the two is that common anode displays has a shared *positive* rail, while common cathode has a shared *ground* rail. This affects how segments are turned on and off. Segments on a common cathode display are turned on by supplying a *positive* voltage to the segment pin; common anode segments are turned on by supplying *ground* on the segment pin (or 0 volts). The pin out varies between different models, so be sure to check the datasheet for the one you're using! 7 segments have been around for a long *long* time, in fact; they predate modern electronics by 60 years! The more you know..

While this post discusses how to control 7 segments with a shift register, there are actually specialized CMOS chips created solely for the purpose of driving these displays (such as the [CD4543](http://www.taydaelectronics.com/cd4543be-cd4543-4543-ic-cmos-bcd-to-7-segment-latches.html)). These are usually called *BCD to 7 segment converters*. A BCD (binary coded decimal) is really just a binary number in the range of 0 to 9 in the decimal system. The converters usually have 8 outputs, one for each of the segments in the display, and 4 main inputs - BCDA. As an example, setting every other input to 1 means that the input as a total is 1010 in binary (9 in decimal), and would result in the outputs A, B, C, F, G activating, which would display a 9 on the display. Input the binary representation on the BCDA input ports, and that number will be displayed on the LED display. Simple! It has one huge drawback though, especially in the case of the CD4543 chip; you can only display numbers from 0 to 9! So what about all the other things we could display on there? There's 128 different configurations on a 7 segment, including 0..9, a few letters, and a few symbols. If we want to utilize all (or, more than the numbers at least) them we need more control than a BCD -> 7 segment chip can offer.

### Shift Registers - What Are They And How Do They Work?
Shift registers *shift* bits along its output ports (of which they usually have 8). What does this mean? Well, if you put 1 bit on it, that bit will dictate the output of port 1. If you put another bit on it in succession, the first bit will be shifted to port 2, and the new bit will end up at port 1. Think of it like a conveyor belt passing through 8 loading bays. So we have 8 trucks (whatever's connected to the outputs) patiently waiting for their load. We put the load on the conveyor belt one box at a time. After placing a box, we move the belt so that the box ends up at the next loading bay. After we've loaded 8 boxes and thus have moved the belt 7 steps, all the trucks can load the boxes, and off they go! But! We need to be able to signal when the boxes are ready to be loaded and carried away, otherwise a truck might end up driving off with a box that should've gone into another truck. So, we'll use a really bright light to indicate if we're done or not. Before we start loading all the boxes, we will turn the light off. Once we've moved all the boxes into position, we'll turn it on to signal the drivers that they're good to go. On our shift register this mechanism is called a *latch*, and similarly to the light in our conveyor belt analogy we need to set the latch to a logical 0 before we start stuffing bits into the register, and once we're done we need to set it to a logical 1. Once the latch is set to 1 the chip will "lock" the state of its outputs to the whatever the state of the bit on the given position is. 

As mentioned in the introduction, the register only requires 3 pins to operate. You've probably already guessed that one of them is to set the latch, but what about the other two? Well, the second one is to set the *clock*, and the third one is to set the *data*. To put a bit into the register we first need to set the data pin to the value we want our new bit to have. Once this is done, we need to send a pulse into the clock pin to let the register know that the current value on the data pin is the value we want. This pulse is in other words responsible for preforming the actual shifting. A pulse is sent by flipping the state of the clock pin for a brief period. 

The chip I'm using for this post is called [74HC595](http://www.taydaelectronics.com/datasheets/A-024.pdf) and is widely available for a quarter or so. I know that some suppliers geared more towards hobbyists charge >$1.00, but personally I wouldn't pay that much since you'd get 4 of them for that price through different vendors (e.g. [tayda](http://www.taydaelectronics.com), [mouser](http://mouser.com)). 

![74HC595 Pinout](/images/74HC595_pinout.png)

### The circuit

**What you need**

| Component | Count |
|---        |---    |
| Resistors, 220-600ohm  |   7    |
| 74HC595 | 1 |
| 7 Segment Display | 1 |
| Arduino | 1 |

**Setting everything up**

For our shift register -> display connection, we'll connect everything in the most intuitive way - out 1 to in a, out 2 to in b, out 3 to in c and so on. **Remember to check the pin out on your 7 segment!** They vary greatly. 

![](/images/shift_segment.png)
*Schematic. Note that I've used the ATMEGA chip, so look at the caption of the pins, not the pin numbers to see what Arduino pin you should use!*

If the schematic doesn't quite do if for you, here's a textual explanation: First, connect pin **8** and **13** on the shift register to **ground**. Pin **16** and **10** goes to **+5V**. Pin **11** goes to Arduino pin **5**, and pin **12** goes to Arduino pin **6**. Lastly, connect pin **14** to Arduino pin **7**.

Now all we need to do is connect the display to our register. The pin output on the shift register is pretty straight forward - output **1** is on pin **15**, **2** is on **1**, **3** is on **2** and so on. Connect output **1** to the **a** input on the display, **2** to **b**, **3** to **c** and so on. But! There's a twist - you need to make the segment connections through a resistor. The actual value of the resistors depends on the specific segment you're using, so be sure to check that.

### The code 

Before we jump into the full listing, allow me to explain how the code is structured and what it does. The most important function is - of course - the `set7seg` function. This is the function that shifts the bits into our register. The `SegValue` constant contains an ordered array of hex values that correspond to different symbols. As an example, the first element contains the hex code to display `0` on the display; the ninth element the hex code to display `9`, and the tenth element contains the hex code to display `A`. Of course you can feed the `set7seg` function with any hex number to display symbols not covered in the constant.

The `shiftOut` function used to "send" the bits to the shift register is part of the Arduino library and simply handles the nitty gritty details of managing the clock pulse and shifting out individual bits. Instead it allows us to shift out a full byte at a time, where each bit contains the state of a pin. The first bit (from right to left) contains the state of pin 1, the second bit contains the state of pin 2 and so on. So shifting out the number `3` will set pin 1 and pin 2 high, since `3` is equal to `0000 0011` in binary (zeros added for clarity). Think of the bit number as the pin number indexed from right to left. You could connect pin 8 on the register to pin a on the segment, pin 7 to pin b and so on if you'd prefer working with it in left to right order (or change MSBFIRST to LSBFIRST in the `set7seg` function), but you'd obviously have to change the `SegValue` constant to reflect the flipped order as well if so.  

The loop code simply cycles through all the symbols defined in the constant using a simple modulus hack.

**If using common anode..**
If you're using a common anode display the on/off states are inverted. A logical 1 will turn the segment off, a logical 0 will turn it off. This also means that you need to invert the values in the `SegValue` constant if you plan on using them. You can do this by using the not (`!`) operator. Also remember that you need to use a resistor (220ohm should be fine) between +5 and the two power inputs on your display!

**Code listing**

``` C Full Listing
//Arduino pin connected to the latch pin (ST_CP) on our 74HC595
#define LATCHPIN    6
//Arduino pin connected to the clock pin (SH_CP) on our 74HC595
#define CLOCKPIN    5
//Arduino pin connected to the data pin (DS) on our 74HC595
#define DATAPIN     7

// This is a list of symbols in our constant and their binary and hex code.
// SYM  H G F E  D C B A   HEX
// 0  - 1 0 1 1  1 1 1 1 - 0xBF
// 1  - 0 0 0 0  0 1 1 0 - 0x06
// 2  - 0 1 0 1  1 0 1 1 - 0x5B
// 3  - 0 1 0 0  1 1 1 1 - 0x4F
// 4  - 0 1 1 0  0 1 1 0 - 0x66
// 5  - 1 1 1 0  1 1 0 1 - 0xED
// 6  - 1 1 1 1  1 1 0 1 - 0xFD
// 7  - 1 0 0 0  0 1 1 1 - 0x07
// 8  - 1 1 1 1  1 1 1 1 - 0xFF
// 9  - 1 1 1 0  0 1 1 1 - 0xE7
// 10 - 1 1 1 1  1 1 1 1 - 0xF7
// 11 - 1 1 1 1  1 1 0 0 - 0xFC
// 12 - 1 1 1 1  1 0 0 1 - 0xF9
// 13 - 0 1 0 1  1 1 1 0 - 0x5E
// 14 - 1 1 1 1  1 0 0 1 - 0xF9
// 15 - 1 1 1 1  0 0 0 1 - 0xF1
// Y  - 0 1 1 0  1 1 1 0 - 0x6E
// H  - 1 1 1 1  0 1 1 0 - 0xF6
// L  - 1 0 1 1  1 0 0 0 - 0xB8
// P  - 1 1 1 1  0 0 1 1 - 0xF3
// -  - 1 1 0 0  0 0 0 0 - 0x40
// _  - 1 0 0 0  1 0 0 0 - 0x08
// -  - 1 0 0 0  0 0 0 1 - 0x01

//Helper constant
const byte SegValue[] = {
  0xBF,
  0x06,
  0x5B,
  0x4F,
  0x66,
  0xED,
  0xFD,
  0x07,
  0xFF,
  0xE7,
  0xF7,
  0xFC,
  0xF9,
  0x5E,
  0xF9,
  0x6E,
  0xF6,
  0xB8,
  0xF3,
  0xC0,
  0x08,
  0x01
};

//Set the value of our display.
//  ex: setting it to 7:
//  set7Seg(SegValue[7]);
void set7Seg(byte num) {
  //Set the latch to low to prepare the write
  digitalWrite(LATCHPIN, LOW);
  //Shift the entire byte into the register.
  //Note that this function preforms a bit-by-bit shift, and also handles the clock pin.
  shiftOut(DATAPIN, CLOCKPIN, MSBFIRST, num);
  //We're done, so set the latch high
  digitalWrite(LATCHPIN, HIGH);
}

void setup() {
  //Prepare pins as outputs
  pinMode(LATCHPIN, OUTPUT);
  pinMode(CLOCKPIN, OUTPUT);
  pinMode(DATAPIN, OUTPUT);
}

unsigned int j = 0;
unsigned int i = 0;

void loop() {
  //Cycle through all the symbols in our SegValue array.
  ++j;
  if (j % 20000 == 1) {
    set7Seg(SegValue[i % 22]);
    i++;
  }
}
```