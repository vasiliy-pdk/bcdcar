#include <Servo.h> 
int const DEBUG = 0;

Servo myservo;  // create servo object to control a servo 

int const LED = 13;

int const SERVO = 3;
int const SERVO_ANGLE_MIN = 0;
int const SERVO_ANGLE_MAX = 178;
int const SERVO_ANGLE_MIDDLE = 89;
int const SERVO_ANGLE_STEP = 5;

int const DCC_ENABLE_PIN = 6;
int const DCC_C1_PIN = 4;
int const DCC_C2_PIN = 5;

int const DCM_SPEED_STEP = 10;

int const MIN_SPEED = 75;
int const MAX_SPEED = 255;

int angle = SERVO_ANGLE_MIDDLE;
int lastAngle = angle;
boolean angleChanged = false;

int motorEnabled = 0;
int motorSpeed = MIN_SPEED;
int lastMotorSpeed = motorSpeed;
int motorDirection = 1;

int addInRange(int var, int addStep, int maxVal, int minVal)
{
  int tempVal = var + addStep;
  if(tempVal <= maxVal && tempVal >= minVal) {
    var = tempVal;
  } else if (tempVal > maxVal) {
    var = maxVal;
  } else if (tempVal < minVal) {
    var = minVal;
  }
  
  return var;
}

int addAngle(int angle, int stepAngle) 
{
  return addInRange(angle, stepAngle, SERVO_ANGLE_MAX, SERVO_ANGLE_MIN);
}

int addSpeed(int speedVal, int stepSpeedVal) {
  return addInRange(speedVal, stepSpeedVal, MAX_SPEED, MIN_SPEED);
}

void setup()
{
  pinMode(DCC_ENABLE_PIN, OUTPUT);
  digitalWrite(DCC_ENABLE_PIN, LOW);
  
  pinMode(DCC_C1_PIN, OUTPUT);
  pinMode(DCC_C2_PIN, OUTPUT);
  
  pinMode(LED,OUTPUT);
  
  Serial.begin(38400);
  myservo.attach(SERVO);
}

void loop()
{
  char temp;
  if (Serial.available() > 0 )
  {
    temp = Serial.read();
    if(temp == '1')
      digitalWrite(LED,HIGH);
    if(temp == '0')
      digitalWrite(LED,LOW);
    if(temp == 'L') {
      angle = addAngle(angle, -SERVO_ANGLE_STEP);
    }
    if(temp == 'R') {
      angle = addAngle(angle, SERVO_ANGLE_STEP);
    } 
    
    if(temp == 'E') {  //enable/disable engine
      motorEnabled = !motorEnabled;
    }
    
    if(temp == 'D') {  //switch direction
      motorDirection = !motorDirection;
      
      analogWrite(DCC_ENABLE_PIN, 0); // protection
      delay(250);
      
      motorSpeed = MIN_SPEED;
    }
    
    if(temp == 'P' && motorEnabled) {  //increase speed
      motorSpeed = addSpeed(motorSpeed, DCM_SPEED_STEP);
    }
    
    if(temp == 'M' && motorEnabled) { //slow the motor
      motorSpeed = addSpeed(motorSpeed, -DCM_SPEED_STEP);
    }
    
    if(temp == 'F') {  //full forward speed
      if(motorEnabled && motorDirection != 1) {
        analogWrite(DCC_ENABLE_PIN, 0); // protection
/*        //break
        digitalWrite(DCC_C1_PIN, HIGH);
        digitalWrite(DCC_C2_PIN, HIGH);
*/        
        delay(300);
      }
      
      motorEnabled = 1;
      motorDirection = 1;
      motorSpeed = MAX_SPEED;
    }
    
    if(temp == 'B') {  //set full backward speed
      if(motorEnabled && motorDirection != 0) {
        analogWrite(DCC_ENABLE_PIN, 0); // protection
/*        //break
        digitalWrite(DCC_C1_PIN, HIGH);
        digitalWrite(DCC_C2_PIN, HIGH);
*/        
        delay(300);
      }
      
      motorEnabled = 1;
      motorDirection = 0;
      motorSpeed = MAX_SPEED;
    }    
    
    if(DEBUG) {
      Serial.print("<-");
      Serial.println(temp);
    }
  }
  
  if(lastAngle != angle) {
    
    if(DEBUG) {
      Serial.print("Angle changed: ");  
      Serial.println(angle);
    }
    
    myservo.write(angle);
    lastAngle = angle;
  }
  
  if(motorDirection == 1) {
    digitalWrite(DCC_C1_PIN, HIGH);
    digitalWrite(DCC_C2_PIN, LOW);
  } else {
    digitalWrite(DCC_C1_PIN, LOW);
    digitalWrite(DCC_C2_PIN, HIGH);
  }
  
  if(motorEnabled) {
    analogWrite(DCC_ENABLE_PIN, motorSpeed);
    if(DEBUG && lastMotorSpeed != motorSpeed) {
      lastMotorSpeed = motorSpeed;
      Serial.print("Motor speed changed = ");
      Serial.println(motorSpeed);
    }
  } else {
    motorSpeed = MIN_SPEED;
    analogWrite(DCC_ENABLE_PIN, 0);
  }
  
}
