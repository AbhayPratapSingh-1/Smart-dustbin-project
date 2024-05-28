# Iot Based waste segregator
smart dustbin
Hardware Components:
Arduino Uno
Two servo motors (for opening the compartments)
Moisture sensor (to detect wet waste)
Ultrasonic sensor (to detect proximity)
Two compartments (for dry and wet waste)
Breadboard and jumper wires
Power supply

code


#include <Servo.h>

// Define pins for the sensors and servos
const int moistureSensorPin = A0;
const int trigPin = 9;
const int echoPin = 10;
const int dryServoPin = 6;
const int wetServoPin = 7;

// Create Servo objects
Servo dryServo;
Servo wetServo;

void setup() {
  Serial.begin(9600);
  
  // Set up the moisture sensor pin
  pinMode(moistureSensorPin, INPUT);
  
  // Set up the ultrasonic sensor pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  
  // Attach servos to the defined pins
  dryServo.attach(dryServoPin);
  wetServo.attach(wetServoPin);
  
  // Set servos to initial positions
  dryServo.write(0);  // Assuming 0 is the closed position
  wetServo.write(0);  // Assuming 0 is the closed position
}

void loop() {
  int moistureValue = analogRead(moistureSensorPin);
  Serial.print("Moisture: ");
  Serial.println(moistureValue);
  
  if (detectProximity()) {
    if (moistureValue > 500) {  // Adjust threshold as needed for wet waste
      openWetCompartment();
    } else {
      openDryCompartment();
    }
  }
  
  delay(1000);  // Adjust delay as needed
}

bool detectProximity() {
  // Send a pulse to trigger the ultrasonic sensor
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Read the pulse duration from the echo pin
  long duration = pulseIn(echoPin, HIGH);
  // Calculate the distance in centimeters
  long distance = duration * 0.034 / 2;

  // Print the distance for debugging
  Serial.print("Distance: ");
  Serial.println(distance);

  // Check if an object is within a certain range (e.g., 20 cm)
  if (distance < 20) {
    return true;
  } else {
    return false;
  }
}

void openDryCompartment() {
  dryServo.write(90);  // Adjust to the correct angle to open
  delay(2000);         // Keep the compartment open for 2 seconds
  dryServo.write(0);   // Close the compartment
}

void openWetCompartment() {
  wetServo.write(90);  // Adjust to the correct angle to open
  delay(2000);         // Keep the compartment open for 2 seconds
  wetServo.write(0);   // Close the compartment
}
