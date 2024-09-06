# Arduino Alarm Clock with DS1307 RTC and Buzzer

## Description

This project implements a simple alarm clock using an Arduino, a DS1307 real-time clock (RTC), and a buzzer. The current time is displayed on a 16x2 LCD screen, and an alarm is set to activate the buzzer at a specific time. The buzzer turns on at 3:15 and turns off at 3:20, serving as a basic alarm system.

## Components

- Arduino Uno
- DS1307 RTC Module
- 16x2 LCD Display
- Buzzer
- Jumper wires
- Breadboard

## Libraries Required

- **DS1307.h**: For communication with the DS1307 RTC module.
- **Wire.h**: For I2C communication.
- **LiquidCrystal.h**: For handling the 16x2 LCD display.

## Wiring Diagram

| Component  | Arduino Pin |
| ---------- | ----------- |
| LCD RS     | 2           |
| LCD Enable | 3           |
| LCD D4     | 4           |
| LCD D5     | 5           |
| LCD D6     | 6           |
| LCD D7     | 7           |
| Buzzer     | 10          |
| DS1307 SDA | A4 (I2C)    |
| DS1307 SCL | A5 (I2C)    |

## Code

```cpp
#if defined(ARDUINO) && ARDUINO >= 100
#include "Arduino.h"
#else
#include "WProgram.h"
#endif

#define buzzer 10
#include <Wire.h>
#include <DS1307.h>
#include <LiquidCrystal.h>

// Initialize the LCD (RS, E, D4, D5, D6, D7)
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
byte jam, menit;

void setup() {
  Serial.begin(9600);
  lcd.begin(16, 2);
  lcd.print("Alarm ARDUINO");
  delay(2000);

  RTC.stop();
  // Set the time on the DS1307 RTC
  RTC.set(DS1307_SEC, 1);
  RTC.set(DS1307_MIN, 23);
  RTC.set(DS1307_HR, 12);
  RTC.set(DS1307_DOW, 4);
  RTC.set(DS1307_DATE, 5);
  RTC.set(DS1307_MTH, 3);
  RTC.set(DS1307_YR, 9);
  RTC.start();

  pinMode(buzzer, OUTPUT);
}

void loop() {
  lcd.setCursor(0, 1);
  lcd.print(RTC.get(DS1307_HR, true));  // Read hour and update values
  lcd.print(":");
  lcd.print(RTC.get(DS1307_MIN, false));  // Read minutes without updating
  lcd.print(":");
  lcd.print(RTC.get(DS1307_SEC, false));  // Read seconds

  jam = RTC.get(DS1307_HR, true);
  menit = RTC.get(DS1307_MIN, false);

  // Check if the current time matches the alarm time (3:15)
  if ((jam == 3) && (menit == 15)) {
    digitalWrite(buzzer, HIGH);  // Turn on the buzzer
  }
  // Turn off the buzzer at 3:20
  else if ((jam == 3) && (menit == 20)) {
    digitalWrite(buzzer, LOW);  // Turn off the buzzer
  }

  delay(1000);  // Update the display and check the time every second
}
```

## How It Works

1. Real-Time Clock (RTC): The DS1307 module keeps track of the current time. It is set up during the initial run, but once set, it maintains the time even if the Arduino is powered off.
2. LCD Display: The current time (hours, minutes, and seconds) is displayed on a 16x2 LCD screen.
3. Alarm Logic: The alarm is set to trigger at 3:15 AM. When the time reaches this point, the buzzer turns on. The buzzer turns off at 3:20 AM.
4. Buzzer Control: The buzzer connected to pin 10 will activate based on the alarm time conditions.

## Setup Instructions

1. Wiring: Connect the LCD, DS1307, and buzzer as per the wiring diagram.
2. Upload Code: Upload the provided code to your Arduino using the Arduino IDE.
3. Alarm Function: Once the code is running, the LCD will display the current time. The alarm will trigger at the specified time (3:15 AM).

## Applications

1. A basic alarm clock for various uses.
2. Demonstration of how to use an RTC module and control external devices based on time.
3. Educational tool for understanding time-based events in microcontroller projects.
