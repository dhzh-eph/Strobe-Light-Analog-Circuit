# Strobe-Light-Analog-Circuit
Analog circuit using a NE555 IC in astable configuration to flicker LEDs at customizable rates with potentiometers. The entire circuit is powered by a 9V alkaline battery. (Work in progress). <br/>

## Strobe Brightness Adjuster (First circuit implementation)
<img width="700" height="555" alt="Screenshot 2026-01-18 220940" src="https://github.com/user-attachments/assets/7d30e985-523d-4e22-9265-92d84b0393f7" /> Schematic 1, 700 x 555 from StrobeLightAnalogCircuit.ms14 <br/>
<img width="300" height="721" alt="image" src="https://github.com/user-attachments/assets/57a52e68-895c-40d6-9b2c-bdc6c7f6e26d" /> Figure 1 <br/>

## Choice implementations
This first implementation allows the potentiometer to adjust the brightness of the LED. To adjust the brightness so that it is consistently bright on one side of the potentiometer’s range and dim on the opposite side, disconnect one of the outer terminals of the potentiometer. This changes the response curve from a peak in the middle to a linear or monotonic progression across the rotation. The isolated wires visible in the prototype were used during testing to evaluate various circuit configurations, helping to determine the cleanest and most reliable layout.

A 10µF capacitor was selected over a 100µF alternative to allow responsive LED brightness control. As larger capacitance values increase the RC time constant, it slows the capacitor's charge and discharge rates. Since the 555 timer's trigger thresholds (1/3Vcc and 2/3Vcc) are fixed, a larger capacitor takes significantly longer to reach these voltages. This excessive filtering would make the LED's brightness changes feel sluggish and less precise when adjusting the potentiometer. <br/>
<img width="300" height="721" alt="image" src="https://github.com/user-attachments/assets/dc8b1b64-4159-4cfa-a0ee-1fc3b8950063" /> Figure 1 annotated <br/>

Now, one may wonder whether a 1uF capacitor could be used, and in this case it definitely could. However, the potentiometer values would require adjusting, as a 1uF capacitor would result in extremely sensitive adjustments to the LED behavior. Also, an electrolytic capacitor was chosen over ceramic because we require energy storage, rather than noise filtering. 

Note that the circuit design should account for the voltage decay of a standard 9V alkaline battery over its operational life. As its output declines (e.g., to 6V or lower), the performance of passive components may require recalibration to maintain optimal circuit function. (This is in the case that there is no constant voltage generator.) 

# Theory and decisions behind pinout placements
<img width="600" height="279" alt="image" src="https://github.com/user-attachments/assets/9b49348d-215b-4eff-a162-9608d2c26ac6" /> [4] NE555 Timer Pinout <br/>

As shown in the picture above, we have trigger (pin 2) and threshold (pin 6) connected to each other directly with a GREEN wire. No resistor is needed since we do not want voltage drop to affect the 2/3Vcc and 1/3Vcc for each comparator in the 555 timer. [2] Next, we have reset (pin 4) and Vcc (pin 8) connected, this is so that the timer can constantly operate without any random resets occuring. 

For discharge (pin 7) and Vcc (pin 8) being connected through a resistor, this is so that discharge can occur when the base of the BJT is given enough voltage. As such the resistors used here prevent component damage from overheating, and the potentiometer allows for the duty cycle to be altered. [2] <br/>
 
<img width="727" height="400" alt="image" src="https://github.com/user-attachments/assets/a9f3fd03-95f2-4187-9d05-816f2ea36fed" /> [2] <br/> 
Exterior Configuration for Astable <br/>

<img width="800" height="445" alt="image" src="https://github.com/user-attachments/assets/c75aa674-0e2f-431d-9106-f576fbede37c" /> [4] NE555 interior BJT orientations <br/>

By connecting threshold (pin 6) and trigger (pin 2) together to a polarity capacitor, while connecting the discharge pin (pin 7) to this node through a resistor, the 555 timer operates in its astable configuration. In this setup, the capacitor voltage repeatedly charges and discharges between 1/3VCC and 2/3VCC. When the capacitor voltage crosses these comparator thresholds, the internal comparators toggle the SR flip-flop, which in turn controls the discharge BJT at pin 7.

This switching action causes continuous oscillation, producing a periodic output waveform at pin 3. The charge and discharge times of the capacitor determine both the oscillation period and duty cycle, resulting in the characteristic on/off pulsing behavior shown in the 555 timer operational diagram and the circuit below.

<img width="782" height="366" alt="image" src="https://github.com/user-attachments/assets/67f311a8-c2c1-4d6f-85b7-d8ed320b2da3" /> [A] Formula to calculate duty cycle and period. <br/>
<img width="798" height="422" alt="image" src="https://github.com/user-attachments/assets/1d012e24-c07c-49d2-bd87-19c463be594d" /> [4] Flip flop table for quick referencing during testing. <br/>

# Second Circuit Implementation
<img width="700" height="508" alt="image" src="https://github.com/user-attachments/assets/cbaf2b85-0d0a-4249-bbed-a44e31d551b5" /> Schematic 2, from StrobeLightAnalogCircuit3Potents.ms14 <br/>
<img src="https://github.com/user-attachments/assets/c97514b8-139f-48ea-9cce-27dd3798662c" width="500" /> Figure 2 <br/>

Two additional potentiometers are added: a second 900k ohm and a 100k ohm. The second 900k ohm potentiometer independently adjusts the pulse width (the ON and OFF times within each cycle), while the 100k ohm potentiometer sets the overall period (the total time of one full cycle). In future revisions, these potentiometers will be standardized to the same resistance value unless a specific design requirement dictates otherwise.

<img width="669" height="840" alt="image" src="https://github.com/user-attachments/assets/742bb47a-0f4f-4952-a780-7fba1219de35" /><br/>
To synchronize multiple LEDs to blink at the same rate as the original, it is more ideal for them to be in series rather than in parallel. A parallel configuration divides the available current, which often leads to inconsistent brightness or some LEDs failing to illuminate. Whereas a series configuration has all the LEDs at similar brightness. However, a series connection is only a temporary solution, as stacking more than three LEDs in series will produce a combined forward voltage that significantly dims them.

![IMG_3261](https://github.com/user-attachments/assets/b489ccf2-dd0e-4c49-8bea-d6fe16edfb81)
An alternating blink pattern can be achieved by connecting one LED to the output (pin 3) and a second LED to the BJT’s collector. However, this configuration presents a limitation: adjusting the brightness of one LED inherently affects the brightness of the other, and overall brightness control is constrained by the available supply voltage. A simplified digital circuit of this image is shown in the StrobeLightAnalogCircuit2.ms14 file.

One other aspect taken into consideration is whether a non-polarity capacitor with resistor could be used for delay. In terms of having a circuit that works, yes it could be used, but it will not achieve the blinking capability that the polarity capacitor can do. As a result, it will simply show one of the LEDS slowly increasing in brightness overtime and then dim down, while the other continues to blink as usual. 

# Third Circuit Implementation
<img width="600" height="934" alt="image" src="https://github.com/user-attachments/assets/2bb101a4-a18c-4835-9917-ff5a3177059e" /><br/>
In this configuration, this results in the red LED allowing change in brightness with the bottom right 100k potentiometer, meanwhile maintaining the brightness of the green LED closest to the BJT.

<img width="400" height="622" alt="image" src="https://github.com/user-attachments/assets/eb8ca181-2995-411b-aaa0-a47e8cea53bc" /><br/>
To verify correctly, we also test with matching LEDs for each respective port. Through this we are able to have it so that one of the LEDs blink and decay with adjustable brightness, while the other LED blinks as pulses, and stays the same brightness. <br/>

# Fourth Circuit Implementation
<img width="500" height="856" alt="image" src="https://github.com/user-attachments/assets/96c2ec1c-a54b-4fc8-9876-22c223f607d7" /><br/>
Here we obtain a circuit that allows for both LEDs to be modified in brightness, while also having alternating blinking patterns. However, the top potentiometer can dim the top red LED but only a bit, while the bottom potentiometer can dim both of the LEDs, dimming the top LED first and then the bottom LED. This is mainly due to more current entering the BJT emitter, so that it can come out the collector when the base takes enough input. As a result, the bottom LED stays powered on longer than the top LED despite it requiring current into the base from the output of the 555 timer.

If we want to implement amplification using a BJT for example, we can do common emitter. 

# References
[1] Image theory https://www.youtube.com/watch?v=iwbGccGU4io <br/>
[2] https://www.youtube.com/watch?v=APghHcA-MOI <br/>
[3] Circuit inspiration https://www.youtube.com/watch?v=euiDv51VVt4 <br/>
[4] I/O table and 555 pinout https://www.youtube.com/watch?v=oZzjmAbyyIQ&t=315s <br/>
[5] Logic Gates https://www.youtube.com/watch?v=sTu3LwpF6XI <br/>
[6] https://www.youtube.com/watch?v=0Z8tslPvU0o <br/>
[7] https://www.youtube.com/shorts/9HOs_BvJ62s

Theory: <br/>
[A] Math equations https://www.youtube.com/watch?v=gTn_HmzXYLo <br/>
[B] https://www.youtube.com/watch?v=OsQObXu4TSA&t=147s <br/>
[C] https://www.youtube.com/watch?v=sWbSeJmUFfw <br/>
[D] https://www.youtube.com/watch?v=EhMk6DUbbPk&t=332s <br/>
