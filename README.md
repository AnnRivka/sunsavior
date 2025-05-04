# sunsavior
Solar Energy Tracker, focuses on automatically adjusting the orientation of solar panels using sensors and microcontrollers to ensure optimal sunlight exposure throughout the day


#include <Servo.h>
#include <LiquidCrystal.h>

// Create servo object to control a servo
Servo myservo; 
int pos = 0; // Variable to store the servo position

// Define pins for LDRs and solar panel
const int ldr1Pin = A0; 
const int ldr2Pin = A1; 
const int solarPanelPin = A2;
const int ledPin = 13;

// Define the pin connections for the LCD
const int voltPin = A3; // Change the analog pin for the voltmeter to A3 to avoid conflict
const int lcdRS = 12; 
const int lcdEN = 11; 
const int lcdD4 = 5; 
const int lcdD5 = 4; 
const int lcdD6 = 3; 
const int lcdD7 = 2; 

// Define the pin for PWM output to voltmeter
const int pwmOutPin = 9; // PWM output pin to drive the voltmeter

// Create an LCD object
LiquidCrystal lcd(lcdRS, lcdEN, lcdD4, lcdD5, lcdD6, lcdD7);

void setup() {
    // Initialize serial communication at 9600 baud rate
    pinMode(ledPin, OUTPUT);
    Serial.begin(9600);
    
    // Attach the servo on pin 9 to the servo object
    myservo.attach(10); // Changed the servo pin to avoid conflict with PWM output
    
    // Initialize the LCD
    lcd.begin(16, 2);
    lcd.clear();
}

void loop() {
    // Read the analog values from LDR1, LDR2, and the solar panel
    digitalWrite(ledPin, HIGH);
    // Wait for 1 second (1000 milliseconds)
    delay(1000);
    
    // Turn the LED off
    digitalWrite(ledPin, LOW);
    // Wait for cased on LDR readings
    if (ldr1Reading > ldr2Reading) {
        // Turn the servo to the right (90 degrees)
        pos = 180; 
        Serial.println("Turn Right"); 
    } else if (ldr2Reading > ldr1Reading) {
        // Turn the servo to the left (0 degrees)
        pos = 0; 
        Serial.println("Turn Left"); 
    } else {
        // No dominant light source, keep servo in neutral position (45 degrees)
        pos = 90; 
        Serial.println("Neutral"); 
    }

    // Write the calculated position to the servo motor
    myservo.write(pos);
    
    // Read the voltage from the voltmeter
    float voltage = analogRead(voltPin) * 5.0 / 1023.0;

    // Map the voltage (0-5V) to the PWM range (0-255)
    int pwmValue = map(solarPanelReading, 0, 1023, 0, 255);

    // Output the scaled PWM value to the PWM output pin
    analogWrite(pwmOutPin, pwmValue);

    // Display the LDR readings and voltage on the LCD
    lcd.clear();
    lcd.setCursor(0, 0); 
    lcd.print("LDR1: "); 
    lcd.print(ldr1Reading); 
    lcd.print(" L2: "); 
    lcd.print(ldr2Reading); 

    lcd.setCursor(0, 1); 
    lcd.print("Volt: "); 
    lcd.print(voltage, 2); // Display voltage with 2 decimal places
    lcd.print("V");
    
    // Print the voltage to the serial monitor for debugging
    Serial.print("Solar Voltage: ");
    Serial.print(voltage);
    Serial.println(" V");

    // Delay for stability
    delay(1000); // Increased delay to allow better readability on LCD
}
