title: MIDI and CV - A Brief History Lesson
categories: 
- synthesizers
tags:
- MIDI
- CV
- synthesizer history
----

A long time ago, in a country not so far away, Robert Moog invented one of the first synthesizers - the Moog. As part of his design, he came up with several circuits that could be controlled by an external signal. This signal could come from a keyboard, or it could be produced by another circuit. By patching together several of these modules with this signal - called control voltage (or CV for short) - a complex signal chain was created, producing delicious, analog sounds. 

CV came to be the standard across most manufactures of synthesizers at the time. One of the uses for CV is to transmit pitch information, which of course is crucial when it comes to making sounds. Most manufacturers used a pitch control system called V/Oct (volts per octave) - though some (mainly Korg and Yamaha synths) used a system called Hz/V (Hertz per volt). In a V/Oct pitch control system 0V represents C0, 1V represents C1 and so on. The other notes are spread linearly between the whole numbers, so e.g. C#0 is 0.0833V, D0 is 0.1666V, and B is 0.9166V. In a Hz/V system an octave is represented by doubling the voltage. So C1 is 1V, C2 is 2V, C3 is 4V, C4 is 8V and so on. For modern modular systems V/Oct is by far the dominant one. 

![Robert Moog](/images/robert_moog.png)
*Robert Moog posing with some of his Moog synthesizers*

CV based systems are extremely flexible. In a modular synth anything can be connected to everything. Sure, sometimes the result would be silence, other times unbearable chaos, but if you wanted to control a filter modulation or a vibrato based on a keyboard, all you had to do was use a patch cable! One major limitation of a control voltage system is that it can only carry one signal at the time. It's simplicity is what makes it powerful, but also what makes it less tidy than modern standards.

In addition to the pitch system, a second control voltage was used; called CV/Gate or CV/Trigger. This signal was a simple on/off system, which was used to turn oscillators or other voltage controlled circuits on or off. So in other words - the pitch control voltage was a constant thing, and the gate/trigger signal dictated whether or not the circuit was active or not. Keyboards would have both a pitch voltage and a trigger/gate voltage. The trigger/gate would be activated when a note was pressed down, and deactivated when it was released. The signal itself was said to be off if the voltage was 0 - on if the voltage was whatever the system ran on.

Back in 1983 digital circuits were becoming increasingly popular, and digital synthesizers started emerging. A replacement for the "obsolete" (in my opinion superior!) control voltage system was deemed necessary to have a more powerful (and standardized) interconnectivity protocol. This replacement was called MIDI, and is still the standard for connecting various pieces of audio equipment (though nowadays it's common to use USB or even ethernet along side the old DIN-5 plugs. It's still MIDI though). MIDI offered several channels of signals - 16 to be exact - and each channel could have several different signals embedded in it. Welcome to the future! Polyphony, isolated channels, separate control signals to control modulations and such - all through a single 5 wire cable. MIDI was quickly incorporated in the first DAWs, which allowed people to arrange much more complex pieces than previously possible using the oldskool analog sequencers which were often limited to 8, 16, or 32 steps that would repeat. For old CV equipment, MIDI to CV converters where introduced. These converters would simply take a digital MIDI signal and convert it to a control voltage which could then be patched into older equipment. 

So let's move onto the present. Currently MIDI is still the dominant standard for any audio equipment interconnectivity. It's used in anything from keyboards, DAW controllers, high-end system 500 modules, synthesizers, and even lighting systems. Still, the old control voltage has its place. There's currently somewhat of an analog revolution going on, in large part due to Korg's success with small, affordable devices like the monotron, monotribe, and volca systems. Most of these devices support both MIDI and control voltage (or can be modified to support it). There's also a very dedicated and very [friendly community](http://www.muffwiggler.com/) of modular synth heads building and using some very impressive setups - all built around analog circuits and controlled by control voltage. The most popular format right now is the Eurorack format, first introduced with the Doepfer A-100 modular system back in '95. 

![Eurorack System](/images/eurorack.png)
*A modern eurorack system. Picture stolen from [synthopia.com](http://synthopia.com).*

Well, that pretty much sums it up. A brief history on automation and control of synthesizers. 