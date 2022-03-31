# Simulation-of-Subsystem-Testing-of-Infusion-Pumps

As part of a Clinical Engineering Project, I was tasked to develop a Standard operating procedure (SOP) to test the various subsystems of the B. Braun Infusomat Space infusion pump to facilitate a more rapid functionality check (i.e., productivity improvement).

Subsystems:
- User interface: Control panel (via push buttons) and LCD.
- Action element: Stepper motor and driver.
- Feedback element: Optical sensor, pressure sensor with LED.


I have written the code in C to test each of these subsystems using Arduino Uno.

Items needed:

1 Stepper motor (28BYJ48)

2 L293DNE Motor Driver IC

3 Alphanumeric 2x16 LCD

4 9V Battery strap

5 Optical block

6 Pin jumper wire (male-to-male)

7 Single core jumper wire

8 Breadboard

9 UNO board with USB cable

10 Pushbutton

11 Trim pot

12 330Ω Resistor

13 1KΩ Resistor

14 5.6KΩ Resistor

15 10KΩ Resistor

16 Crocodile clips

17 LEDs

18 Component X: Pressure sensor coupled with an LED (OEM Part number: 34521372)


Functions Of The Subsytems:
1. User Interface: Control panel (via push button & LCD)
- The LCD screen simulates the LCD screen on the original infusion pump.
- The push button simulates the START/STOP buttons in the user interface of the infusion pump.

2. Feedback Element: Optical Sensor and Component X
- Optical Sensor simulates that of the original infusion pump to detect if the door of the infusion pump has been closed.
- Component X simulates the yellow LED light on the infusion pump and the pressure sensor to detect if the tubing has been inserted into the pump. If it is inserted, the pressure sensor  senses this and LED switches off.

3. Action Element: Stepper motor and Driver
- Simulates the stepper motor and its driver within the infusion pump after the door has been closed. The stepper motor controls the peristaltic fingers (multiple rollers compressing the tube), which enable/control pumping motion and downstream flow. The motor allows user to precisely control the peristaltic pump speed and direction. This gives greater controllability & accuracy.


Step-by-Step Testing Instruction of Subsystems: (Refer to the code comments and Flowchart if necessary)

1. User Interface: Control panel (via push button & LCD)
- First, in the void setup, program will display “Welcome” message on LCD & prompt user to press the button. This ensures LCD connections are correct.
- When user presses the button (button’s status is read as HIGH), LCD screen should display “Block the Optical Sensor!” (As shown in the void loop). This tests for the button and ensures it is working properly, & correctly connected to LCD.
(At the very end, the same push button also stops the stepper motor from moving. So, only one button is used throughout the whole procedure. This means less confusion as we do not have to differentiate between different buttons.)

2. Feedback Element: Optical Sensor and Component X (which is the pressure sensor + LED)
- Next, when user blocks optical sensor with an opaque object, it would detect this (State drops from >800 to <100) & send signals back to display “Door Opened” & “Comp X LED on” on LCD. LED of Component X lights up. This means optical sensor is working properly & senses the decrease in intensity of light. (If the blockage is lifted away from the optical sensor (State is > 800), it similarly detects this & continue to prompt user to block optical sensor again.)
- User must press the pressure sensor on Component X to switch off LED on the Component X. Once pressed, pressure sensor detects the change (StateX > 200), & sends signals back. LCD displays “Comp X LED Off” and “Unblk Opt Sensor”, prompting user to unblock the optical sensor. This ensures Component X works properly and tests if it senses pressure.

3. Action Element: Stepper motor and Driver
- Lastly, when optical sensor is unblocked, it senses that again (State > 800). The controller sends signals to the motor driver to drive the stepper motor. The stepper motor then starts running, meaning it works properly. We set only 1 motor pin "HIGH" at a time. We also included a time delay. This would be repeated in the code until all the 4 motor pins are turned "HIGH" sequentially. (If optical sensor is blocked again, motor stops and LCD prompts user to “unblk optical sensor” again.) The status of the button is read again to see if the motor is stopped by the user. If it is not, stepper motor continues running. This continues until the user presses the button (User Interface), which would stop the stepper motor from running and cause the LCD to display “END”.


For Component X, we use Digital Read for LED and Analog Read for pressure/force sensor because….

digitalRead() reads the value from a specified digital pin, either HIGH or LOW (or binary values of 1 and 0). Its application can be used in ascertaining whether an LED is on or a push button is engaged. LED & push buttons only have 2 states (On/Off, 1/0, etc.)

The only pins you can analogRead() on are those with a preceding 'A': A0, A1, A2, and A3. Instead of simply returning HIGH or LOW, analogRead() returns a number between 0 and 1023 -- 1024 possible analog values. An output of 0 equates to 0V, and 1023 means the pin reads 5V. They cover a wide range of values. As opposed to having only 2 states for digital, analogs possess multiple states and can have many possibilities. So, for our pressure sensor (it doesn’t just have 2 states HIGH and LOW), it has a range of values when pressed/released, we would need to use the analog input to monitor the changes in pressure.

All About Component X:
- Made of pressure sensor & LED
- Programmed such that when it is pressed (pressure is applied onto sensor), the LED turns off
- Mimics the detection of insertion of tubing into infusion pump, to detect when it has been inserted and if it has been done so properly
Red wire (Pin 1) to 5V, White wire (Pin 4) to GND, Black wire (Pin 3) to pressure sensor & the other White wire (Pin 2) to LED.
