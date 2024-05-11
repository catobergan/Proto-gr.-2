//Neopixel løpelys
#include <Adafruit_NeoPixel.h>

#define LED_PIN 2

#define LED_COUNT 78

Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);

//pins
const int startButtonPin = 4;
const int startLedPin = 3;
const int piezoPin = 5;
const char lightSensorPin[] = {A0,A1,A2,A3,A4,A5};
const int buttonPin[] = {7,8,9,10,11,12,13}; //button pins
const int brennpunktLedsPin[] = {6,7,8,9,10,11};

//variables
const int ropeLength = 20000;
const int timerLength = 15000;
const int ropeTimerLength = 15;
const int rope1Length = 20;
const int rope2Length = 20;
const int interval = 1000;

const int leftLed = 0;
const int middleLed1 = 9;
const int middleLed2 = 10;
const int rightLed = 19;

const int left1Led = 15;
const int middle1Led1 = 24;
const int middle1Led2 = 25;
const int right1Led = 34;

const int left2Led = 35;
const int middle2Led1 = 44;
const int middle2Led2 = 45;
const int right2Led = 54;

const int rammeLed1 = 55;

//variables
int status = 0; //0=pause/1=stage1/2=stage2 modus
int timer = 0;
int t = 0;
int lightSensorLim[6]; //amoount of light needed to activate light sensor

//bool variables
bool startJustPressed = false;
bool pauseJustActivated = true;
bool rope1Burned = false;
bool rope2Burned = false;
bool rope1AlreadyBurned = false;
bool rope2AlreadyBurned = false;

bool ropeTimerLeds[] = {1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1};
bool rope1Leds[] = {1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1};
bool rope2Leds[] = {1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1};

bool brennpunkt[] = {0,0,0,0,0,0}; //første tre er rope1, siste tre er rope2
bool buttonPressed[] = {0,0,0,0,0,0}; //første tre er rope1, siste tre er rope2, chacks if button has been pressed
bool brennpunktLock[] = {0,0,0,0,0,0}; //første tre er rope1, siste tre er rope2, prevent rapid switch of value by holding button

//funktions
void setFyr(int i); //aktiverer brennpunkter
void burn(); //brenner tauet
void finish(); //code to run when attampt has been completed
void winner();
void fail();

void setup() {
  strip.begin();           // INITIALIZE NeoPixel strip object (REQUIRED)
  strip.clear();
  for (int i = 0; i <= right2Led; i++) {
    strip.setPixelColor(i, 150, 0, 0);
  }
  strip.show();            // Turn OFF all pixels ASAP
  
  //set pinMode for lightSensor;
  for(int i = 0; i < 6; i++){
    pinMode(lightSensorPin[i], INPUT);
  }

  // set pinMode for brennpunktLeds
  for(int i = 0 ; i < 6 ; i++) {
    pinMode(brennpunktLedsPin[i], OUTPUT);
  }

  pinMode(startButtonPin, INPUT);
  pinMode(startLedPin, OUTPUT);
  pinMode(piezoPin, OUTPUT);

  //calibrate photoresistors
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 150);
  }
  strip.show();

  for (int j = 0; j < 5; j++) {
    for (int i = 0; i < 6; i++) {
      lightSensorLim[i] += analogRead(lightSensorPin[i]);
    }
    delay(100);
  }
  for (int i = 0; i < 6; i++) {lightSensorLim[i] = lightSensorLim[i] / 5 + 175;}
}

void loop() {
  //pause mode
  switch (status) {
  case 0:
    t++;

    //run once after switching to pause
    if (pauseJustActivated) {
      t = 0;
      //lock certain brennpunkt
      if (!rope1Leds[leftLed]) {
        brennpunktLock[0] = true;
      }
      if (!rope1Leds[middleLed1] || !rope1Leds[middleLed2]) {
        brennpunktLock[1] = true;
      }
      if (!rope1Leds[rightLed]) {
        brennpunktLock[2] = true;
      }
      if (!rope2Leds[leftLed]) {
        brennpunktLock[3] = true;
      }
      if (!rope2Leds[middleLed1] || !rope2Leds[middleLed2]) {
        brennpunktLock[4] = true;
      }
      if (!rope2Leds[rightLed]) {
        brennpunktLock[5] = true;
      }

      //ramme lys pause
      for (int i = rammeLed1; i < LED_COUNT; i++) {
        strip.setPixelColor(i, 0, 150, 0);
      }
      strip.show();

      digitalWrite(startLedPin, HIGH);

      pauseJustActivated = false;
    }
    
    //brennpunktLeds
    /*for (int i = 0 ; i < 6 ; i++){
        if (!brennpunktLock[i]) {
          if (t == 0) {
            digitalWrite(brennpunktLedsPin[i], HIGH);
          } else if (t == 50) {
            digitalWrite(brennpunktLedsPin[i], LOW);
          } else if (t == 100) {
            digitalWrite(brennpunktLedsPin[i], HIGH);
          } else if (t == 50) {
            digitalWrite(brennpunktLedsPin[i], LOW);
          } else if (t == 100) {
            digitalWrite(brennpunktLedsPin[i], HIGH);
          }
        } else {
          digitalWrite(brennpunktLedsPin[i], LOW);
        }
      }*/

    //lytt etter brennpunkt input
    for (int i = 0; i < 6; i++) {

      if (!buttonPressed[i] && analogRead(lightSensorPin[i]) > lightSensorLim[i]) {
        delay(5);
        if (analogRead(lightSensorPin[i]) > lightSensorLim[i]) {
          buttonPressed[i] = true;
          brennpunkt[i] = !brennpunkt[i];
        
          setFyr(i); 
        }
      } else if (buttonPressed[i] && !(analogRead(lightSensorPin[i]) > lightSensorLim[i])) {
        buttonPressed[i] = false;
      }
    }

    //start button
    if (digitalRead(startButtonPin)) {
      startJustPressed = true;
      status = 1;
    }
    delay(1);
    break;

  case 1: //not pause mode
    //run once after start is pressed
    if (startJustPressed) {
      ropeTimerLeds[0] = false;
      strip.setPixelColor(0, 0, 0, 0);
      strip.show();

      //check if rope has already been burned
      for (int i = 0; i < rope1Length; i++) {
        if (rope1Leds[i]) {
          break;
        } else if (i == rope1Length - 1) {
          rope1AlreadyBurned = true;
        }
      }
      for (int i = 0; i < rope2Length; i++) {
        if (rope2Leds[i]) {
          break;
        } else if (i == rope2Length - 1) {
          rope2AlreadyBurned = true;
        }
      }

      //ramme lys start
      for (int i = rammeLed1; i < LED_COUNT; i++) {
        strip.setPixelColor(i, 150, 0, 0);
      }
      strip.show();

      digitalWrite(startLedPin, LOW);

      startJustPressed = false;
    }
    
    delay(interval); //time between iteration
    burn(); // burns rope

    //check if rope1 & rope2 has burned up
    if (!rope1AlreadyBurned) {
      for (int i = 0; i < rope1Length; i++) {
        if (rope1Leds[i]) {
          break;
        } else if (i == rope1Length - 1) {
          rope1Burned = true;
        }
      }
    }

    if (!rope2AlreadyBurned) {
      for (int i = 0; i < rope2Length; i++) {
        if (rope2Leds[i]) {
          break;
        } else if (i == rope2Length - 1) {
          rope2Burned = true;
        }
      }
    }

    //check if it is time to pause
    if (rope1AlreadyBurned && rope2AlreadyBurned) {
      status = 0;
      pauseJustActivated = true;
      fail();
      finish();
    }
    else if (!ropeTimerLeds[ropeTimerLength - 1] && rope1Burned && rope2Burned) {
      status = 0;
      pauseJustActivated = true;
      winner();
      finish();
    } else if (!ropeTimerLeds[ropeTimerLength - 1]) {
      status = 0;
      pauseJustActivated = true;
      fail();
      finish();
    } else if (rope1Burned && rope2Burned) {
      status = 0;
      pauseJustActivated = true;
      fail();
      finish();
    } else if (!rope1AlreadyBurned && !rope2AlreadyBurned && (rope1Burned || rope2Burned)) {
      status = 0;
      pauseJustActivated = true;
    }
    break;
  }
}

void setFyr(int i) { //skrur på led tilsvarende aktivert brennpunkt
  if (i == 0 && !brennpunktLock[i]) {
    if (rope1Leds[leftLed]) {
      rope1Leds[leftLed] = false;
      strip.setPixelColor(left1Led, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else {
      rope1Leds[leftLed] = true;
      strip.setPixelColor(left1Led, strip.Color(150, 0, 0));
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  } else if (i == 1 && !brennpunktLock[i]) {
    if (rope1Leds[middleLed1] && rope1Leds[middleLed2]) {
      rope1Leds[middleLed1] = false;
      rope1Leds[middleLed2] = false;
      strip.setPixelColor(middle1Led1, 0, 0, 0);
      strip.setPixelColor(middle1Led2, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else if (!rope1Leds[middleLed1] && !rope1Leds[middleLed2]) {
      rope1Leds[middleLed1] = true;
      rope1Leds[middleLed2] = true;
      strip.setPixelColor(middle1Led1, 150, 0, 0);
      strip.setPixelColor(middle1Led2, 150, 0, 0);
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  } else if (i == 2 && !brennpunktLock[i]){
      if (rope1Leds[rightLed]) {
      rope1Leds[rightLed] = false;
      strip.setPixelColor(right1Led, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else {
      rope1Leds[rightLed] = true;
      strip.setPixelColor(right1Led, 150, 0, 0);
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  } else if (i == 3 && !brennpunktLock[i]) {
    if (rope2Leds[leftLed]) {
      rope2Leds[leftLed] = false;
      strip.setPixelColor(left2Led, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else {
      rope2Leds[leftLed] = true;
      strip.setPixelColor(left2Led, 150, 0, 0);
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  } else if (i == 4 && !brennpunktLock[i]) {
    if (rope2Leds[middleLed1] && rope2Leds[middleLed2]) {
      rope2Leds[middleLed1] = false;
      rope2Leds[middleLed2] = false;
      strip.setPixelColor(middle2Led1, 0, 0, 0);
      strip.setPixelColor(middle2Led2, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else if (!rope2Leds[middleLed1] && !rope2Leds[middleLed2]) {
      rope2Leds[middleLed1] = true;
      rope2Leds[middleLed2] = true;
      strip.setPixelColor(middle2Led1, 150, 0, 0);
      strip.setPixelColor(middle2Led2, 150, 0, 0);
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  } else if (i == 5 && !brennpunktLock[i]) {
    if (rope2Leds[rightLed]){
      rope2Leds[rightLed] = false;
      strip.setPixelColor(right2Led, 0, 0, 0);
      digitalWrite(brennpunktLedsPin[i], HIGH);
    } else {
      rope2Leds[rightLed] = true;
      strip.setPixelColor(right2Led, 150, 0, 0);
      digitalWrite(brennpunktLedsPin[i], LOW);
    }
  }
  strip.show();
  delay(100);
}

void burn() { //skrur på led ved siden av en allerede tent led. 
  
  //copy ropeTimerLeds, rope1Leds & rope2Leds
  bool ropeTimerLedsCopy[ropeTimerLength];
  bool rope1LedsCopy[rope1Length];
  bool rope2LedsCopy[rope2Length];
  
  //make copy of ropeTimerLeds
  for (int i = 0; i < ropeTimerLength; i++) {
    ropeTimerLedsCopy[i] = ropeTimerLeds[i];
  }

  //make copies of rope1/2Leds
  for (int i = 0; i < rope1Length; i++) {
    rope1LedsCopy[i] = rope1Leds[i];
    rope2LedsCopy[i] = rope2Leds[i];
  }

  //run timer
  for (int i = 1; i < ropeTimerLength; i++) {
    if (ropeTimerLeds[i]){
      ropeTimerLedsCopy[i] = false;
      strip.setPixelColor(i, 0, 0, 0);
      break;
    }
  }

  //burn rope1
  for (int i = 0; i < rope1Length; i++) {
    if (rope1Leds[i]){
      if ((i == 0 && !rope1Leds[i + 1]) || (i == 19 && !rope1Leds[i - 1])) {
        rope1LedsCopy[i] = false;
        strip.setPixelColor(i + left1Led, 0, 0, 0);
      } else if (i != 0 && i != 19 && (!rope1Leds[i + 1] || !rope1Leds[i - 1])) {
        rope1LedsCopy[i] = false;
        strip.setPixelColor(i + left1Led, 0, 0, 0);
      }
    }
    //burn rope2
    if (rope2Leds[i]){
      if ((i == 0 && !rope2Leds[i + 1]) || (i == 19 && !rope2Leds[i - 1])) {
        rope2LedsCopy[i] = false;
        strip.setPixelColor(i + left2Led, 0, 0, 0);
      } else if (i != 0 && i != 19 && (!rope2Leds[i + 1] || !rope2Leds[i - 1])) {
        rope2LedsCopy[i] = false;
        strip.setPixelColor(i + left2Led, 0, 0, 0);
      }
    }
  }
  
  //paste strip(1,2,3)LedsCopy to strip(1,2,3)Leds
    for (int i = 0; i < ropeTimerLength; i++) {
    ropeTimerLeds[i] = ropeTimerLedsCopy[i];
  }
  for (int i = 0; i < rope1Length; i++) {
    rope1Leds[i] = rope1LedsCopy[i];
    rope2Leds[i] = rope2LedsCopy[i];
  }

  strip.show();
}

void finish() {  
  for (int i = 0; i < 6; i++) {
    brennpunktLock[i] = false;
    digitalWrite(brennpunktLedsPin[i], LOW);
  }
  
  for (int i = 0; i <= right2Led; i++) {
    ropeTimerLeds[i] = true;
    strip.setPixelColor(i, 150, 0, 0);
    rope1Leds[i] = true;
    strip.setPixelColor(i + left1Led, 150, 0, 0);
    rope2Leds[i] = true;
    strip.setPixelColor(i + left2Led, 150, 0, 0);
  }
  strip.show();
}

void winner() {
  int noteLength = 400;
  tone(piezoPin, 196);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength);
  tone(piezoPin, 261);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 150, 0);
  }
  strip.show();
  delay(noteLength);
  tone(piezoPin, 329);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength);
  tone(piezoPin, 392);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 150, 0);
  }
  strip.show();
  delay(noteLength);
  noTone(piezoPin);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength);
}

void fail() {
  int noteLength = 340;
  tone(piezoPin, 164);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 150, 0, 0);
  }
  strip.show();
  delay(noteLength);
  noTone(piezoPin);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength/2);
  tone(piezoPin, 146);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 150, 0, 0);
  }
  strip.show();
  delay(noteLength);
  noTone(piezoPin);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength/2);
  tone(piezoPin, 130);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 150, 0, 0);
  }
  strip.show();
  delay(noteLength);
  noTone(piezoPin);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 0, 0, 0);
  }
  strip.show();
  delay(noteLength/2);
  tone(piezoPin, 98);
  for (int i = rammeLed1; i < LED_COUNT; i++) {
    strip.setPixelColor(i, 150, 0, 0);
  }
  strip.show();
  delay(noteLength*2);
  noTone(piezoPin);
  delay(noteLength/2);
}
