#include <LiquidCrystal.h> // include the library code:
LiquidCrystal lcd(13, 12, 11, 10, 9, 8);    // initialize the library with the numbers of the interface pins. LCD pins.
void opticalblk(void);  //function prototypes
void opticalunblk(void);
int button = 7;       // input pin for button
int PhotoIn = A5;     //input pin for optical sensor
int prevState = 0;  int buttonstate = 0;    // variables for button. buttonstate is the variable for reading pushbutton's status.
int State = 0;       // a variable to read the encoder state for optical sensor
int count = 0; int num = 0;      //using count/num to control the flow of the various tests
int Xin = A1; //input pin for Component X (Pressure Sensor)    
int Xin2 = 2; //input pin for Component X (LED --> 1.0 means it is "ON")
int StateX = 0; //variable to read the state for pressure sensor
int motorPin1 = 3; //input pins for motor driver
int motorPin2 = 4;
int motorPin3 = 5;
int motorPin4 = 6;

void setup()                   //initialising pins as input & output.
{
  pinMode(button, INPUT);      //set pin 7 as input (button)
  pinMode(PhotoIn, INPUT);     //set pin A5 as input (opticalsensor)
  lcd.begin(20, 4);
  lcd.print("WELCOME");            
  lcd.setCursor(0,1);                                                                                                                                                                                  
  lcd.print("PRESS THE BUTTON");
  Serial.begin(9600);   //starts serial communication/opens serial port. "9600 baud" means the serial port is able to transfer a maximum of 9600 bits per second (bps). Data rate is being set to 9600 bps.
  pinMode(Xin, INPUT);  //set pin A1 as input (Component X Pressure Sensor)                                                                                                                                                                                                                                                                                   
  pinMode(Xin2, INPUT); //set pin A2 as input (Component X LED)
  pinMode(motorPin1, OUTPUT);  //sets pin 3, 4, 5, 6, which pins connected to motor driver, as output.
  pinMode(motorPin2, OUTPUT);
  pinMode(motorPin3, OUTPUT);
  pinMode(motorPin4, OUTPUT);
}

void loop ()                    //Press = buttonstate is high, Don't press = buttonstate is low. By default, buttonstate is low because we used a pull-down configuration for our button: 
                                //When the push button is idle, the input of the microcontroller is a Logic 0 as it is connected to ground via the pull down resistor. (With reference to https://electronicguidebook.com/do-push-buttons-need-resistors/)
{
 buttonstate = digitalRead(button);            //Reads the status of button (if it's high or low or pressed/released).      
 if (buttonstate != prevState && count == 0)     //Compares it with previous state: If previous state and button state are different and count is 0, it enters block. 1. Press button; buttonstate high, prev state low  2. Release button; buttonstate low, prev state high
 {
  if (buttonstate == HIGH)     //If the encoder output is in a high logical state; if button is pressed = button state becomes high. 1. Will enter this block since buttonstate high   2. Will not enter this block since buttonstate low
  {
    opticalblk();              //calls function void opticalblk(void) to display message to block optical sensor.
  }
  prevState = buttonstate;    // 1: buttonstate high, prev state becomes high.  2: buttonstate low, prev state also becomes low. ("REFRESHES" back to original state since both states are now low).
  count = count + 1;          //increase count by 1. To fulfil conditions later (allows things to happen only when button has been pressed)
 }

State = analogRead(PhotoIn); // reads the status of optical sensor (to determine if it's blocked or unblocked)
Serial.println(State);       // allows display of the state of optical sensor in serial monitor
if (State < 800 && count == 1)  // check status of optical sensor if it is less than 800 (meaning blocked). count == 1 condition ensures that button has to be pressed once for this to happen.
{
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Door Opened");
  lcd.setCursor(0,1);
  lcd.print("Comp. X LED ON");
  delay(1000);
  digitalWrite(Xin2, HIGH);  //switch on LED on Component X when "door is opened".
} 
if (State > 800 && count == 1) //if optical sensor isn't blocked
{
  opticalblk();      //go back to displaying "block optical sensor" message by calling function again. 
  digitalWrite(Xin2, LOW);    //switch off LED on Component X if optical sensor becomes unblocked.
  delay(1000);              
}

while (State < 800 && count == 1)           //When optical sensor is blocked
{
  State = analogRead(PhotoIn);  //continously reads the status of optical sensor: The moment the blockage is removed, the LCD screen message changes back and LED switches off.
  StateX = analogRead(Xin);    //Read the state of pressure sensor in Component X
  Serial.println(StateX);      //Allows display of State X in serial monitor
  if (StateX > 200)     //If there is pressure on pressure sensor/if user presses it
  {
    opticalunblk();   //Calls function void opticalunblk(void) to display message to unblock optical sensor.
    digitalWrite(Xin2, LOW);      //Switch off LED on Component X.
    delay(1000);
  }
  while (StateX > 200)       //after the pressure sensor has been pressed
  {
    State = analogRead(PhotoIn);
    if (State > 800)        //if optical sensor has been unblocked
    {                                     
      lcd.clear();                      
      lcd.setCursor(0,0);
      lcd.print("Press button");  
      lcd.setCursor(0,1);
      lcd.print("To Stop Motor!");
      digitalWrite(motorPin4, HIGH);  //Stepper motor starts running. Sets only 1 motor pin "HIGH" at a time.
      digitalWrite(motorPin3, LOW);
      digitalWrite(motorPin2, LOW);
      digitalWrite(motorPin1, LOW);
      delay(10);                      //Includes a time delay.
      digitalWrite(motorPin4, LOW);   
      digitalWrite(motorPin3, HIGH);  //Repeats this until all the 4 motor pins are turned "HIGH" sequentially.
      digitalWrite(motorPin2, LOW);
      digitalWrite(motorPin1, LOW);
      delay(10);
      digitalWrite(motorPin4, LOW);
      digitalWrite(motorPin3, LOW);
      digitalWrite(motorPin2, HIGH);
      digitalWrite(motorPin1, LOW);
      delay(10);
      digitalWrite(motorPin4, LOW);
      digitalWrite(motorPin3, LOW);
      digitalWrite(motorPin2, LOW);
      digitalWrite(motorPin1, HIGH);
      delay(10);
      buttonstate = digitalRead(button);     // Reads the status of the button again to see if the motor is stopped by the user. If it is not, stepper motor continues running.
      while(buttonstate == HIGH)           // If button is pressed.        Using 'if' instead of 'while' does not work as I have to hold the button to stop the stepper motor.
      {  
        lcd.clear();                        //stepper motor stops running and displays END message.
        lcd.setCursor(0,0);
        lcd.print("END OF TEST!!!!!!");
        lcd.setCursor(0,1);
        lcd.print("#YAYYYY!!!!!!");
        delay(1000);  
      }                                    
    }   
    if (State < 800)        //If optical sensor remains blocked, or if optical sensor becomes blocked again.
    {
      opticalunblk();       //Stepper motor does not run. Continues displaying the message to unblock sensor by calling the function.
      delay(1500);
    }
  }
}
}
void opticalblk(void)
{
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Block the");       //displays message to block the optical sensor.
  lcd.setCursor(0,1);
  lcd.print("Optical Sensor!");    
}
void opticalunblk(void)
{
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Comp. X LED off");  //displays message to unblock the optical sensor.
  lcd.setCursor(0,1);
  lcd.print("Unblk Opt Sensor!");
}