#include <Servo.h>
#include <SoftwareSerial.h>

#define motorApwm  5 //silnik napędowy 
#define motorBpwm  3 //silnik poduszki
#define motorA1 6 //kierunek 1 
#define motorA2 7 //kierunek 2

Servo servo;
char state;
int vSpeed = 240;
const byte rxPin = 9;
const byte txPin = 8;
SoftwareSerial BTSerial(rxPin, txPin); // RX TX
int PWM = 0;


void setup() {
  Serial.begin(9600);
  pinMode(motorApwm, OUTPUT);
  pinMode(motorBpwm, OUTPUT);
  pinMode(motorA1, OUTPUT);
  pinMode(motorA2, OUTPUT);
  servo.attach(10);
  servo.write(90);
  pinMode(rxPin, INPUT);
  pinMode(txPin, OUTPUT);
  BTSerial.begin(9600);

}

void jazda(short int pwmA,bool M1A, bool M1B)
{
  analogWrite(motorApwm, pwmA);
  digitalWrite(motorA1, M1A);
  digitalWrite(motorA2, M1B);
}

void loop() {
  if (BTSerial.available() > 0) {
  state = (char) BTSerial.read();
  /*Wlaczenie poduszki*/
  if (state == 'W') {
  PWM=100;
   while(PWM<=130)
     {
      analogWrite(motorBpwm, PWM);
      PWM=PWM+1;
      delay(50);
     }
     delay(50);
    
    while(PWM<=200)
      {
        analogWrite(motorBpwm, PWM);
         PWM=PWM+1;
        delay(50);
      }
      
      PWM=250;
      analogWrite(motorBpwm, PWM);
  }
  /*Wylaczenie poduszki*/
  else if (state == 'w') {
    while(PWM>0){
    PWM = PWM-1; 
    analogWrite(motorBpwm, PWM);
    delay(10);
    }
    PWM = 0;
    analogWrite(motorBpwm, PWM);
  }
  /*Ustalenie prędkości wirnika napedu */
  if (state == '0') {
    vSpeed = 0;
  } else if (state == '1') {
    vSpeed = 100;
  } else if (state == '2') {
    vSpeed = 120;
  } else if (state == '3') {
    vSpeed = 140;
  } else if (state == '4') {
    vSpeed = 160;
  } else if (state == '5') {
    vSpeed = 180;
  } else if (state == '6') {
    vSpeed = 190;
  } else if (state == '7') {
    vSpeed = 210;
  } else if (state == '8') {
    vSpeed = 225;
  } else if (state == '9') {
    vSpeed = 245;
  } else if (state == 'q') {
    vSpeed = 255;
  }

  /*Kierunek do przodu*/
  if (state == 'F') {
    jazda(vSpeed, 0, 1);
    servo.write(90);
  }
  /*Kierunek do przodu w lewo*/
  else if (state == 'G') {
    jazda(vSpeed, 0, 1);
    servo.write(40);
  }
  /*Kierunek do przodu w prawo*/
  else if (state == 'I') {
    jazda(vSpeed, 0, 1);
    servo.write(140);
  }
  /*W lewo*/
  else if (state == 'L') {
    jazda(vSpeed, 0, 1);
    servo.write(40);

  }
  /*w prawo*/
  else if (state == 'R') {
    jazda(vSpeed, 0, 1);
    servo.write(140);
  }
 
  /*zatrzymanie */
  else if (state == 'S' or state == 'D') {
    jazda(0, 0, 0);
    servo.write(90);
    
  }
}
}
