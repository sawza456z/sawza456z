#include <SharpIR.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

#define model 1080

#define INPUT_A A0
#define INPUT_B A1
#define LED_PIN 13

SharpIR SharpIR_A(INPUT_A, model);
SharpIR SharpIR_B(INPUT_B, model);

unsigned int in = 0, out = 0;
unsigned long time_A, time_B ,time_led;

#define SET_DISTANCE 60

void setup() {
  pinMode(LED_PIN,OUTPUT);
  Serial.begin(9600);
  lcd.begin();
  lcd.display();
  lcd.backlight();
  lcd.clear();
  lcd_print();
  time_led = millis();
}

void loop() {
  if (SharpIR_A.distance() < SET_DISTANCE && SharpIR_B.distance() > SET_DISTANCE) {
    time_A = millis();
    while (millis() - time_A < 1000) {
      if (SharpIR_B.distance() < SET_DISTANCE) {
        in++;
        while (SharpIR_A.distance() < SET_DISTANCE) {
          delay(300);
        }
        lcd_print();
        break;
      }
    }
  }
  if (SharpIR_B.distance() < SET_DISTANCE && SharpIR_A.distance() > SET_DISTANCE) {
    time_B = millis();
    while (millis() - time_B < 1000) {
      if (SharpIR_A.distance() < SET_DISTANCE) {
        out++;
        while (SharpIR_B.distance() < SET_DISTANCE) {
          delay(300);
        }
        lcd_print();
        break;
      }
    }
  }
  if(millis() - time_led > 1000){
    time_led=millis();
    digitalWrite(LED_PIN,!digitalRead(LED_PIN));
  }
}

void lcd_print() {
  lcd.setCursor(4, 0);
  lcd.print("COUNTER");
  lcd.setCursor(0, 1);
  lcd.print("IN = ");
  lcd.print(in);
  lcd.print(" ");
  lcd.print("OUT = ");
  lcd.print(out);
}
