# MWSA-FacePlate
MicroWave Spectrum Analyzer software for the 3GHz Spectrum Analyzer

This design is intended to meet the following preliminary specifications:

- [x] Frequency Range: 23.5 to 3000 MHz.
- [ ] Spurious Free Dynamic Range: 70 dBc
- [ ] Powered either by External +7 ~ +13V (Schottky Diode OR'd)
- [ ] Arduino driver provided allowing for serial command & control
- [x] Dual XTAL Reference allows minimizing Frac-N spurs.
- [x] Individual AZ1117 low-noise regulators for Digital, RF and VCO
- [x] Compatible with various Arduinos using the UNO pin arrangement
- [ ] Max RF Input +10dBm DC-48MHz ; +15 dBm 48-3000MHz
- [ ] IF Bandwidths : A:~ 300KHz B:~ 7.5 kHz (Dual simultaneous IF's)

To Do's
- [x] Determine Reference source,(i.e. Crystal Oscillator module at Frequency X)
- [ ] Determine Shielding/Grounding requirements for PCB layout
- [x] Add information to the spread-sheet cost estimate (columns G and H).

PROJECT#3 MAX2871 Synthesizer Tests 24June2020 WN2A

Notes:

   The MAX2871 does give stable output measured on my Frequency Counter (23.5 to 500MHz+) and also viewed on an old HP8558B Spectrum Analyzer to over 1500 MHz. Power was about +3dBm average. Spurious could not be measured accurately, due to equipment limitations, but looks better than -60 dBc.
      
   Both Laptops (Dell Mini Netbook and MacBook Pro) ran FreeBasic Host programs on Puppy Linux Slacko version 6.3.2. Both Laptops could deliver 115200 Baud Commands via the USB bus, with or without any delay added in the Host program. The Host program/Laptops showed no sign of breakup, and the Arduino Rx LED stays lit, regardless of the delay setting from 0 to 10 milliseconds. My suspicion is with my Arduino Sketch MAX2871_Load_Word_115200_1.ino (code snippet below):

   I commented out the serial receive lines above. While this code can receive each byte, and perform the byte->char->Integer conversions quickly and without any issues, the problem may be in identifying which is the first byte in a 32-bit word. The way this seems to work is that for a delay of ~5-6 milliseconds between words, it has no problem. The "blank time" easily allows for buffer clearing. Reducing this blank time to <5 milliseconds results in overwhelming the serial buffer (with this sketch code). To be sure, I do not use the Serial.print commands in this loop structure, and I do not enable the serial monitor. That would have made matters worse. Possibly, there is better Arduino code that can be used here, so sweep times can be possibly improved toward ~3 milliseconds/point.
   
   The Shift Out Routine (Shifty(x)) was profiled on a scope and found to be plenty fast. More accurate measurements of this will be looked into, but the serial receive port code is much more suspect.

   Another little quirk noticed: In order for the Host Program to get control of the Arduino, this procedure was needed:
   * Connect Arduino to Laptop via USB cable
   * Open the Arduino IDE, check the Serial Port # (0,1,2,etc)
   * Open the Serial Monitor. Now close the Serial Monitor and IDE.
   * Run the FB program, either one B) or C) above.
  
   You didn't need to load any sketches or enter anything into the Serial Monitor, but it seemed weird that you need to open the IDE AND the Serial Monitor.
			
   Some thoughts about obtaining faster sweep rates by reducing USB communication and having the Arduino do the Fractional-N calculations. These are very computation heavy functions, being floating point and long unsigned data. It's very easy and efficient done in FB code, because of a 32 (or 64 bit) operating system, 64 bit processor. This	was verified in speed profiles, showing that the FB code was plenty fast. That was true	on the Dell Mini Netbook also.

   The addition of spur-reduction code will make computation more resource-hungry, both in program memory and speed. So while communication is faster when off-loading code to the Arduino, it will result in slower sweep times from a slower processor. It seems to be much easier to fix the Arduino sketch  1A) to get faster sweeps -OR- see if other Arduino boards have quicker serial peripherals, keeping in mind peripheral performance does not always track processor clock speeds.
		
Summary: From the standpoint of an inexpensive synthesizer ($11.53) with inexpensive Arduino controllers (~$9.95 or less ) providing a good 23.5-6000 MHz sweep generator with reasonable sweep times is very impressive. A slight improvement toward ~3 millisecond step time should make it very useful, especially for the PROJECT#1.
