title: Arduino and DACs - a love story
categories: 
- electronics
- arduino
tags:
- MCP4922
----

There comes a time in every tinkerers life when the urge to output an analog signal from a digital circuit becomes unbearable. In the case of Arduino one could always use the PWM outputs, but alas, their 8-bit resolution often falls short. This post is all about how to use an external DAC chip to offer not only a higher resolution, but also a more stable output. I'll be using the [MCP4922](http://www.mouser.com/ds/2/268/21897a-70809.pdf) chip in the examples - a fairly cheap (comparably) 12-bit 2 channel DAC with an external voltage reference. The MCP4922 supports SPI, making it pretty straight forward to work with.

### So what exactly is a DAC?
A DAC (Digital to Analog Converter) is a neat little chip that allows us to set an arbitrarily output voltage based on a digital input. DACs work in steps, meaning that the digital input value does not map 1 to 1 with the output value. In the case of an 8-bit DAC the input value will be a number between 0..255, and the output will be a voltage from 0..VSS. Send in 0 - get 0v out. Send in 255, get VSS out. Send in 128, get VSS/2 out. You get the point. For a 12-bit DAC - like the MCP4922 - the input number is from 0 to 4095, giving us 4095 steps between 0v and VSS.

DACs depend on a reference voltage. The reference voltage is a stable supply that specifies the maximum output voltage. It's very important that this source is as clean as possible to avoid fluctuations on the output. Some DACs has a built-in reference voltage - others rely on an external circuit to provide it. For the latter it's usually better to use a regulator designed to serve as a reference rather than a regular regulator. 

So what can you do with it? Well, it gives you a way to control any analog circuit with a microcontroller. I think that's pretty neat, no? To give a more concrete example, they're great for MIDI to V/Oct converters. [You can read about MIDI and V/Oct in my other post here](). 

### Alright, got it. But how do I use it?
I'm glad you asked! The MCP4922 supports SPI which is easy to use with Arduino. SPI, or Serial Peripheral Interface Bus, is pretty much the de facto standard for full duplex communication between chips. 

So that covers the digital part of it. What of the analog? Well, in addition to the SPI/serial interface, the chip has two reference voltage inputs, and two analog outputs. 

The first thing we need to do is figure out which pins are where. So without further ado, I present to you the pinout:

![MCP4922](/images/mcp4922.png)

As you can see, it's pretty standard fare. Pin 1 goes to VDD, pin 12 to ground. Output 1 is on pin 14, output 2 on pin 10. 

### Enough of this rambling, show me the code!

