# Arduino---joyStick-check-dot-possition

# Description:
Arduino---Joystick-Target-Finder-on-LCD

This project is a mini interactive game using an Arduino, a joystick module, an LCD display, two LEDs, and a buzzer.

At the start of each round, the program randomly selects a hidden target location on the LCD screen. This target position is printed to the Serial Monitor for debugging or reference.

The player moves a dot on the LCD using the joystick. When the joystick button is pressed, the program checks if the current dot position matches the hidden target:

If the position matches:

The blue LED lights up

The buzzer plays a success tone


If the position is incorrect:

The red LED lights up

The buzzer plays a failure tone


In both cases, the system resets and selects a new random target on the screen.

This project is a fun way to learn about random number generation, joystick input, screen coordinate logic, and interactive feedback using Arduino components.



# Pictures:
!(img1)[Arduino_project_joyStick_find_correct_position_PART1.jpeg]
!(img2)[Arduino_project_joyStick_find_correct_position_PART2.jpeg]
!(img3)[Arduino_project_joyStick_find_correct_position_PART3.jpeg]

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

int btnState = HIGH;

int analogXread, analogYread;
byte lastX, lastY;
byte x, y;
int btnValue;
byte randomX, randomY;
byte correctDotCordiant[2];

void setup() {
  randomSeed(analogRead(A2));
  randomX = random(0, 16);
  randomY = random(0, 2);
  correctDotCordiant[0] = randomX;
  correctDotCordiant[1] = randomY;
  lcd.init();
  lcd.backlight();
  pinMode(redLED, OUTPUT);
  pinMode(blueLED, OUTPUT);
  pinMode(buzzer, OUTPUT);
  pinMode(joyBTN, INPUT_PULLUP);
  Serial.begin(9600);
}

void loop() {
  Serial.print("Correct cordiants= ");
  Serial.print(randomX);
  Serial.println(randomY);

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
      digitalWrite(blueLED, HIGH);
      digitalWrite(buzzer, HIGH);
      delay(1000);
      digitalWrite(blueLED, LOW);
      digitalWrite(buzzer, LOW);
    }

    else{
      Serial.println("Wrong!");
      
      for (byte i=0; i<2; i++){
        digitalWrite(redLED, HIGH);
        digitalWrite(buzzer, HIGH);
        delay(300);
        digitalWrite(redLED, LOW);
        digitalWrite(buzzer, LOW);
        delay(500);
      }

    }

    
    delay(1000);

    dotCordiant[0] = 0;
    dotCordiant[1] = 0;
    randomX = random(0, 16);
    randomY = random(0, 2);
    correctDotCordiant[0] = randomX;
    correctDotCordiant[1] = randomY;

    count = 0;

  }


  delay(100);
}

```
