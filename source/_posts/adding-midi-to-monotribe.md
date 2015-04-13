title: Adding MIDI to a monotribe
categories:
- electronics
tags:
- MIDI
- PCB
---

I got the Monotribe a few years ago, when it was brand new. I hadn't used it much, mostly because it seemed somewhat limited as an actual musical instrument. It is however, very simple to add MIDI capabilities to it and I finally got around to it.

So let's take a look.

![Monotribe MIDI](images/monotribe_midi.jpg)

This is where you can hook up a midi input. I recommend using the connector on the other side of the board instead, but I was lazy and didn't have the right kind laying around so I just soldered the wires directly to the existing pads. The red one is VCC, the black one GND, and the yellow one the RX input.

Here's the circuit I used for decoupling the input from the monotribe circuit board:

![MIDI input](images/midi_in_circuit.png)

I also did a quick layout for it:

![MIDI input](images/midi_in.png)

..which got turned into this by the fine folks at [OSH Park](http://oshpark.com) (I have several other uses for this board too, so figured I'd get a proper one made):

![MIDI in board](images/midi_in_board.jpg)

![MIDI in board populated](images/midi_in_board_populated.jpg)

I of course managed to mess up the silkscreen on the outputs (reads TX instead of RX plus messy positioning), but I have fixed it in the below files. One minor issue though - the solder pads for the resistors ended up being really frickin' small. They were easy enough to solder though so I haven't changed the package (it's a 207/12 IIRC).

And yes - I was totally too lazy to file away the panel leftovers on the board before populating it. :p It works at least.

[Eagle schematics](files/monotribemidi.sch)
[Eagle PCB layout](files/monotribemidi.brd)



