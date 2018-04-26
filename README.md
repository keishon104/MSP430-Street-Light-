# MSP430-Street-Light-




#include <LiquidCrystal.h>
#include "RTC_A.h"
// to the pins used:
const int analogInPin = P6_0;  // Analog input pin that the LDR is attached to
const int analogOutPin = P2_7; // Analog output pin that the LED is attached to

int inputValue = 0;        // value read from the LDR
int outputValue = 0;        // value output to the PWM (analog out)
int dimValue = 100;           // values to turn LED dim

int led2 = 2;


const unsigned long    updateInterval =  1;     // seconds between updates
volatile unsigned long updateTime =      0;      // seconds since last update



// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(P2_0, P2_2, P2_3, P2_4, P2_5, P2_6);




void setup()
{
  // set up the LCD's number of columns and rows: 
  lcd.begin(16, 2);
  // Print a message to the LCD.
//  lcd.print("hello, world!");
  
  // Second, Minute, Hour, Day, Day of Week, Month, Year);
  RTC_init(50, 59, 1, 26, THURSDAY, 4, 2018);

  // call the function Update() every second
  RTC_enableRTCInterrupt(Update); 

  pinMode(led2, OUTPUT);
  Serial.begin(9600);
  Serial.println("Starting RTC");
}


void loop(){

    printDateAndTime();
  delay(1000);
  int clockSet = RTC_getHour();
  int x = clockSet;
    // read the analog in value:
      int readValue = analogRead(analogInPin);
         inputValue = readValue/40.95;
        // read the analog in value:
      // map it to the range of the analog out:
      outputValue = map(inputValue, 0, 100, 0, 255);
      // change the analog out value:


   if(inputValue >= 90 && (x >=21 || x< 2)) { // SWITCH on the light when light is 90 percent

         Serial.println("LED light on");
      digitalWrite(led2,HIGH);
      analogWrite(analogOutPin, dimValue);
  }
  else if (x>=2 && x<=6)  { // dim the light when time is 2-6am
       Serial.println("LED light DIM 1");
       int dimValue1 = 50;
       analogWrite(analogOutPin, dimValue1);
    }


  else if (inputValue<=80){
    int offValue = 0;
        analogWrite(analogOutPin, offValue);
  }
  {
//      delay(1000);
  }
  // print the results to the serial monitor:
  lcd.setCursor(0, 1);
  Serial.print("sensor = " );
  Serial.print(inputValue);
  Serial.print("\t output = ");
  Serial.println(outputValue);
  
  lcd.print("sen=" );
  lcd.print(inputValue);
  lcd.print(" out=");
  lcd.print(outputValue);

  // wait 500 milliseconds before the next loop
  // for the analog-to-digital converter to settle
  // after the last reading:
  //delay();
}



void printDateAndTime(){

  Serial.print(RTC_getYear());
  Serial.print("/");
  Serial.print(RTC_getMonth());
  Serial.print("/");
  Serial.print(RTC_getDay());
  Serial.print("  ");
  Serial.print(RTC_getHour());
  Serial.print(":");
  Serial.print(RTC_getMinute());
  Serial.print(":");
  Serial.println(RTC_getSecond());

  lcd.setCursor(0, 0);
  lcd.print(RTC_getYear());
  lcd.print("/");
  lcd.print(RTC_getMonth());
  lcd.print("/");
  lcd.print(RTC_getDay());
  lcd.print(" ");
  lcd.print(RTC_getHour());
  lcd.print(":");
  lcd.print(RTC_getMinute());
  lcd.print(":");
  lcd.print(RTC_getSecond());

}

void Update(void){
    updateTime++;
  
}
