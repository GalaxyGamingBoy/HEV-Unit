> [!warning] 
> This project messes with relatively **high voltage** (~400VDC) please keep that in mind, it can seriously hurt you.
## June 10, 2025
I always found the concept of logging what we can't see or perceive interesting and as such I always found weather and physics interesting. So I always had this idea in the back of my mind about being able to record it and maintaining a safe environment.  
  
The idea for the project is simple, build a simple weather/radiation detector station that monitors the environment and logs the information. It will also feature a geiger counter and volatile gas detector as such to alert the inhabitants of dangerous situations. It will feature a display with telemetry stats as well as a web monitoring software for remote access.

The modules:
1. Temperature
2. Pressure
3. Volatile Gas
4. Geiger CPM
5. Telemetry

## June 11, 2025
The microcontroller of choice in this project will be the Pico W, as it offers a plethora of GPIO ports as well as good enough ADC, and a WiFi connection for telemetry. For starters I hooked up the power.

### Power Supply
The HEV Unit (or Hazardous Environment Unit) will be powered by 2x AA or a DC Barrel Jack that accepts up to ~12VDC using the HT7850-A.

The battery pack will also have a toggle switch as to be able to put the unit into offline mode.

![[Screenshot 2025-06-13 at 11.07.25 PM.png]]![[Screenshot 2025-06-13 at 11.07.38 PM.png]]

### Status
Of course, especially in a telemetry focused unit like this that reaches high voltage (Geiger Tubes usually need ~400VDC) the board will feature 2 LEDs that display the power status and as well as the high voltage control.

![[Screenshot 2025-06-13 at 11.09.30 PM.png]]

## June 12, 2025
Adding some more of the fundamental features of the projects now, the display, display controls and the buzzer for the geiger counter.

For the display after a lot of market research and looking for specifically a I2C display, I ended up choosing a DFRobot panel that has enough space for it to display enough information in a not so cramped way.

To control the display I opted to go with a joystick, the UI will be controlled by the joystick's 2 axis rotation whilst the action will be used by the joystick being pressed in. Hall effect joystick is unnecessary for the quite limited usage this will see, so a cheapo potentiometer based joystick should work fine.

Oh, almost forgot, for the environment data sensor I chose a BME680, quite pricey but it combines all of the functionality and features I envision in a relative small package so i won't complain too much. Furthermore it can be communicated using a I2C connection, which is ideal, considering the RP2040 has 2 I2C busses we can use. One of the bus will be for the TFT display and the other for the BME680.

![[Screenshot 2025-06-13 at 11.13.25 PM.png]]![[Screenshot 2025-06-13 at 11.15.58 PM.png]]

## June 18, 2025
Been busy with finals and classes but got the opportunity to work on this project again. This time I need to mess with the big scary part, the actual Geiger Muller Tube, aka the heart of this project.   
  
According to the translated documentation of the GM tube, to drive it correctly I need roughly ~400V DC. To drive it, I plan to step up 3.3V to 400V. The good part in this story is that Geiger Tubes are considered open circuits and need very little current, so a buck boost step up converter that charges for some time ought to be enough.

![[Screenshot 2025-06-19 at 1.01.20 AM.png]]

Until this project I have not messed with boost converters at all so this took quite a while of research on how to do them safely and their basic principals. Basically we will use a 555 clock to pulse the transistor fast enough to generate the appropriate voltage. In our design the cut-off voltage will be set by the potentiometer to allow for more granual control.

> **Basics of Boost Converters**
> 1. The transistor switches on/off rapidly (in our case driven by a timer)
> 
> 2. 
> 	1. When the transistor is **on**, current builds in the inductor.
>
> 	2. When the transistor is **off**, the inductor releases energy through the diode to charge the capictor.
> 
> 4. Repeating this process steps up the voltage to our potentiometer target.

Now here is the schematic, slightly inspired by other setups to make sure it works. We still need to setup the High Voltage LED but that's for later.

![[Screenshot 2025-06-19 at 1.48.54 AM.png]]

## June 19, 2025
Hopefully the scary part, the high voltage circuit, is done now. The next thing on the plan is the appropriate detection logic, so we can actually count the detections. The basics of the detection logic are simple, first the geiger voltage passes through a voltage divider and onto a transistor. When a particle is detected, the transistors "pulses" and triggers a hardware interrupt on the microcontroller. Now, words are good but let's see it in action.

![[Screenshot 2025-06-19 at 2.06.17 AM.png]]
![[Screenshot 2025-06-19 at 2.09.02 AM.png]]

That wasn't that bad logic wise, only if the generation was as good.  
Now let's work on the high voltage led. I want the LED to remain active for a little while after powering down to ensure there is absolutely no chance of a shock hazard. As such I came up with this design. It uses a diode and a capacitors as a one way limited time current source.

![[Screenshot 2025-06-19 at 2.25.31 AM.png]]

![[Screenshot 2025-06-19 at 2.25.05 AM.png]]
