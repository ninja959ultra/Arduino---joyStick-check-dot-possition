# Arduino---joyStick-check-dot-possition


# Code Below:
```cpp
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

int pinX = A1;
int pinY = A0;
byte joyBTN = 2;
byte count = 0;
byte blueLED = 10;
byte redLED = 12;
byte buzzer = 7;

byte dotCordiant[2];
byte correctDotCordiant[2] = {3, 0};

int btnState = HIGH;

int analogXread, analogYread;
byte lastX, lastY;
byte x, y;
int btnValue;

void setup() {
  lcd.init();
  lcd.backlight();
  pinMode(redLED, OUTPUT);
  pinMode(blueLED, OUTPUT);
  pinMode(buzzer, OUTPUT);
  pinMode(joyBTN, INPUT_PULLUP);
  Serial.begin(9600);
}

void loop() {
  analogXread = analogRead(pinX);
  analogYread = analogRead(pinY);
  btnValue = digitalRead(joyBTN);

  x = map(analogXread, 0, 1023, 0, 16);
  y = map(analogYread, 0, 1023, 0, 2);

  
  if (x != lastX || y != lastY) {
    lcd.setCursor(lastX, lastY);
    lcd.print(' '); 


    lcd.setCursor(x, y);
    lcd.print('.');

    lastX = x;
    lastY = y;
  }

  if (btnValue == LOW & btnState == HIGH) {
    count++;
    dotCordiant[0] = x;
    dotCordiant[1] = y;
  }

  if (count == 1){
    if (dotCordiant[0] == correctDotCordiant[0] && dotCordiant[1] == correctDotCordiant[1]){
      Serial.println("Correct!");
    }

    else{
      Serial.println("Wrong!");
    }

    delay(2000);
    count = 0;
  }


  delay(50);
}

```
