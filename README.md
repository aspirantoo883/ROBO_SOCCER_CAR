/******** Bluetooth Robot Remote Control
   ESP32 + L298N Motor Driver
***************************************/

#include <BluetoothSerial.h>

BluetoothSerial SerialBT;

// ========== MOTOR PINS ==========
const int enA = 16;  // Right motor speed
const int in1 = 17;  // Right motor direction
const int in2 = 18;
const int in3 = 19;  // Left motor direction
const int in4 = 21;
const int enB = 22;  // Left motor speed

// ========== OTHER PINS ==========
const int buzPin = 23;
const int ledPin = 4;
const int wifiLedPin = 2; // Status LED

// ========== VARIABLES ==========
int SPEED = 255;
int speed_Coeff = 3;

const int freq = 1000;
const int resolution = 8;

// ========== SETUP ==========
void setup() {
  Serial.begin(115200);

  Serial.println("Starting Bluetooth Robot...");

  pinMode(buzPin, OUTPUT);
  pinMode(ledPin, OUTPUT);
  pinMode(wifiLedPin, OUTPUT);

  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);

  digitalWrite(buzPin, LOW);
  digitalWrite(ledPin, LOW);
  digitalWrite(wifiLedPin, LOW);

  // ESP32 PWM setup (Arduino-ESP32 core 3.x)
  ledcAttach(enA, freq, resolution);
  ledcAttach(enB, freq, resolution);

  Stop();

  // Start Bluetooth Classic
  SerialBT.begin("RoboSoccer");

  Serial.println("Bluetooth is ready!");
  Serial.println("Pair with: RoboSoccer");
}

// ========== MAIN LOOP ==========
void loop() {
  if (SerialBT.available()) {
    char command = SerialBT.read();

    Serial.print("Received: ");
    Serial.println(command);

    switch (command) {
      case 'F': Forward();       break;
      case 'B': Backward();      break;
      case 'R': TurnRight();     break;
      case 'L': TurnLeft();      break;
      case 'G': ForwardLeft();   break;
      case 'H': BackwardLeft();  break;
      case 'I': ForwardRight();  break;
      case 'J': BackwardRight(); break;
      case 'S': Stop();          break;

      case 'V': BeepHorn();      break;
      case 'W': TurnLightOn();   break;
      case 'w': TurnLightOff(); break;

      case '0': SPEED = 80;  refreshSpeed(); break;
      case '1': SPEED = 100; refreshSpeed(); break;
      case '2': SPEED = 120; refreshSpeed(); break;
      case '3': SPEED = 135; refreshSpeed(); break;
      case '4': SPEED = 150; refreshSpeed(); break;
      case '5': SPEED = 170; refreshSpeed(); break;
      case '6': SPEED = 190; refreshSpeed(); break;
      case '7': SPEED = 205; refreshSpeed(); break;
      case '8': SPEED = 220; refreshSpeed(); break;
      case '9': SPEED = 240; refreshSpeed(); break;
      case 'q': SPEED = 255; refreshSpeed(); break;
    }
  }
}

// ========== SPEED CONTROL ==========
void refreshSpeed() {
  ledcWrite(enA, SPEED);
  ledcWrite(enB, SPEED);
}

// ========== MOVEMENT FUNCTIONS ==========
void Forward() {
  refreshSpeed();
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void Backward() {
  refreshSpeed();
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void TurnRight() {
  refreshSpeed();
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void TurnLeft() {
  refreshSpeed();
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void ForwardRight() {
  ledcWrite(enA, SPEED);
  ledcWrite(enB, SPEED / speed_Coeff);

  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void ForwardLeft() {
  ledcWrite(enA, SPEED / speed_Coeff);
  ledcWrite(enB, SPEED);

  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void BackwardRight() {
  ledcWrite(enA, SPEED);
  ledcWrite(enB, SPEED / speed_Coeff);

  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void BackwardLeft() {
  ledcWrite(enA, SPEED / speed_Coeff);
  ledcWrite(enB, SPEED);

  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void Stop() {
  ledcWrite(enA, 0);
  ledcWrite(enB, 0);

  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

// ========== ACCESSORIES ==========
void BeepHorn() {
  digitalWrite(buzPin, HIGH);
  delay(120);
  digitalWrite(buzPin, LOW);
}

void TurnLightOn() {
  digitalWrite(ledPin, HIGH);
}

void TurnLightOff() {
  digitalWrite(ledPin, LOW);
}