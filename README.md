# Line-follower
Line follower code without PID 

#define trig 7
#define echo 8

int edge = 800;
int leftMotor[] = {3, 2};
int rightMotor[] = {5, 4};
int irPin[] = {A0, A1, A2, A3};
bool irOutput[] = {0, 0, 0, 0};

int leftDistance;

void updateDistance() {
  Serial.println("Updating distances: ");
  int dua;
  digitalWrite(trig, HIGH);
  delayMicroseconds(1000);
  digitalWrite(trig, LOW);
  dua = pulseIn(echo, HIGH);
  leftDistance = (dua / 2) / 29.1;
  Serial.println("Left: " + String(leftDistance) + " cm");
}

void setMotors(int l, int r) {
  if (l >= 0 && r >= 0) {
    analogWrite(leftMotor[0], l);
    digitalWrite(leftMotor[1], LOW);
    analogWrite(rightMotor[0], r);
    digitalWrite(rightMotor[1], LOW);
  }
  else if (l < 0 && r < 0) {
    l = -l;
    r = -r;
    analogWrite(leftMotor[1], l);
    digitalWrite(leftMotor[0], LOW);
    analogWrite(rightMotor[1], r);
    digitalWrite(rightMotor[0], LOW);
  }
  else if (l < 0 && r > 0) {
    l = -l;
    analogWrite(leftMotor[1], l);
    digitalWrite(leftMotor[0], LOW);
    analogWrite(rightMotor[0], r);
    digitalWrite(rightMotor[1], LOW);
  }
  else if (l > 0 && r < 0) {
    r = -r;
    analogWrite(leftMotor[0], l);
    digitalWrite(leftMotor[1], LOW);
    analogWrite(rightMotor[1], r);
    digitalWrite(rightMotor[0], LOW);
  }
  else {
    analogWrite(leftMotor[0], LOW);
    digitalWrite(leftMotor[1], LOW);
    analogWrite(rightMotor[0], LOW);
    digitalWrite(rightMotor[1], LOW);
  }
}

void stopa() {
  setMotors(0, 0);
  delay(5);
}
void forword() {
  setMotors(180, 150); //(5,4)
}
void sright() {
  setMotors(100, 200); //(0,4)
  delay(5);
}
void right() {
  setMotors(0, 200); //(0,4)
  delay(5);
}
void sleft() {
  setMotors(200, 100); //(5,0)
  delay(5);
}
void tright() {
  setMotors(0, 200); //(0,4)
  delay(1000);
}
void tleft() {
  setMotors(200, 100); //(5,0)
  delay(1000);
}
void left() {
  setMotors(200, 0); //(5,0)
  delay(5);
}

void setup() {
  Serial.begin(9600);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  for (int i = 0; i < 2; i++) {
    pinMode(leftMotor[i], OUTPUT);
    pinMode(rightMotor[i], OUTPUT);
  }
  for (int i = 0; i < 4; i++) {
    pinMode(irPin[i], INPUT);
  }
}

void loop() {
  updateDistance();
  for (int i = 0; i < 4; i++) {
    for (int j = 0; j < i; j++) Serial.print("  ");
    if (analogRead(irPin[i]) >= edge) irOutput[i] = 1;
    else irOutput[i] = 0;
    Serial.print(String(analogRead(irPin[i])));
    Serial.print("--->" + String(irOutput[i]));
    Serial.print("\n");
  }
  Serial.print("\n");

  if (irOutput[0] == 0 && irOutput[1] == 0 && irOutput[2] == 0 && irOutput[3] == 0) {
    stopa();
  }
  //{
  //}
  //    if (leftDistance >= 2 && leftDistance <= 15) {
  //      if (leftDistance < 8) left();
  //      else if (leftDistance > 8) right();
  //      else forword();
  //    }
  //    else stop();
  //  }
  else if (irOutput[0] == 0 && irOutput[1] == 0 && irOutput[2] == 0 && irOutput[3] == 1) right();
  else if (irOutput[0] == 0 && irOutput[1] == 0 && irOutput[2] == 1 && irOutput[3] == 1) right();
  else if (irOutput[0] == 0 && irOutput[1] == 0 && irOutput[2] == 1 && irOutput[3] == 0) sright();
  else if (irOutput[0] == 0 && irOutput[1] == 1 && irOutput[2] == 1 && irOutput[3] == 0) forword();
  else if (irOutput[0] == 0 && irOutput[1] == 1 && irOutput[2] == 0 && irOutput[3] == 0) sleft();
  else if (irOutput[0] == 1 && irOutput[1] == 1 && irOutput[2] == 0 && irOutput[3] == 0) left();
  else if (irOutput[0] == 1 && irOutput[1] == 0 && irOutput[2] == 0 && irOutput[3] == 0) left();
  else if (irOutput[0] == 1 && irOutput[1] == 1 && irOutput[2] == 1 && irOutput[3] == 0) tleft();
  else if (irOutput[0] == 0 && irOutput[1] == 1 && irOutput[2] == 1 && irOutput[3] == 1) tright();
  else if (irOutput[0] == 1 && irOutput[1] == 0 && irOutput[2] == 0 && irOutput[3] == 1) stopa();
  else if (irOutput[0] == 1 && irOutput[1] == 1 && irOutput[2] == 1 && irOutput[3] == 1) forword();
}
