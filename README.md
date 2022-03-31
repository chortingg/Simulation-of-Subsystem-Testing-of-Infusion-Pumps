# Simulation-of-Subsystem-Testing-of-Infusion-Pumps

As part of a Clinical Engineering Project, I was tasked to develop a Standard operating procedure (SOP) to test the various subsystems of the B. Braun Infusomat Space infusion pump to facilitate a more rapid functionality check (i.e., productivity improvement).

Subsystems:
- User interface: Control panel (via push buttons) and LCD.
- Action element: Stepper motor and driver.
- Feedback element: Optical sensor, pressure sensor with LED.


I have written the code in C to test each of these subsystems using Arduino Uno.


Functions Of The Subsytems:
1. User Interface: Control panel (via push button & LCD)
- The LCD screen simulates the LCD screen on the original infusion pump.
- The push button simulates the START/STOP buttons in the user interface of the infusion pump.

2. Feedback Element: Optical Sensor and Component X
- Optical Sensor simulates that of the original infusion pump to detect if the door of the infusion pump has been closed.
- Component X simulates the yellow LED light on the infusion pump and the pressure sensor to detect if the tubing has been inserted into the pump. If it is inserted, the pressure sensor  senses this and LED switches off.

3. Action Element: Stepper motor and Driver
- Simulates the stepper motor and its driver within the infusion pump after the door has been closed. The stepper motor controls the peristaltic fingers (multiple rollers compressing the tube), which enable/control pumping motion and downstream flow. The motor allows user to precisely control the peristaltic pump speed and direction. This gives greater controllability & accuracy.


Step-by-Step Testing Instruction of Subsystems: (Refer to the code comments) 

1. User Interface: Control panel (via push button & LCD)
- First, in the void setup, program will display “Welcome” message on LCD & prompt user to press the button. This ensures LCD connections are correct.
- When user presses the button (button’s status is read as HIGH), LCD screen should display “Block the Optical Sensor!” (As shown in the void loop). This tests for the button and ensures it is working properly, & correctly connected to LCD.
(At the very end, the same push button also stops the stepper motor from moving. So, only one button is used throughout the whole procedure. This means less confusion as we do not have to differentiate between different buttons.)

2. Feedback Element: Optical Sensor and Component X (which is the pressure sensor + LED)
- Next, when user blocks optical sensor with an opaque object, it would detect this (State drops from >800 to <100) & send signals back to display “Door Opened” & “Comp X LED on” on LCD. LED of Component X lights up. This means optical sensor is working properly & senses the decrease in intensity of light. (If the blockage is lifted away from the optical sensor (State is > 800), it similarly detects this & continue to prompt user to block optical sensor again.)
- User must press the pressure sensor on Component X to switch off LED on the Component X. Once pressed, pressure sensor detects the change (StateX > 200), & sends signals back. LCD displays “Comp X LED Off” and “Unblk Opt Sensor”, prompting user to unblock the optical sensor. This ensures Component X works properly and tests if it senses pressure.

3. Action Element: Stepper motor and Driver
- Lastly, when optical sensor is unblocked, it senses that again (State > 800). The controller sends signals to the motor driver to drive the stepper motor. The stepper motor then starts running, meaning it works properly. We set only 1 motor pin "HIGH" at a time. We also included a time delay. This would be repeated in the code until all the 4 motor pins are turned "HIGH" sequentially. (If optical sensor is blocked again, motor stops and LCD prompts user to “unblk optical sensor” again.) The status of the button is read again to see if the motor is stopped by the user. If it is not, stepper motor continues running. This continues until the user presses the button (User Interface), which would stop the stepper motor from running and cause the LCD to display “END”.
