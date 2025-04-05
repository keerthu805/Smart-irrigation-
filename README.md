#include <LiquidCrystal.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

// Initialize the library with the numbers of the interface pins
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

DHT dht(DHTPIN, DHTTYPE);

const int moistureSensorPin = A0;
const int pumpPin = 8;
const int buzzerPin = 9;
const int canvasPin = 10;

void setup() {
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  dht.begin();
  
  pinMode(moistureSensorPin, INPUT);
  pinMode(pumpPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(canvasPin, OUTPUT);
  
  lcd.print("Smart Irrigation");
  delay(2000);
  lcd.clear();
}

void loop() {
  int moistureLevel = analogRead(moistureSensorPin);
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Check if any reads failed and exit early (to try again).
  if (isnan(humidity) || isnan(temperature)) {
    lcd.setCursor(0, 0);
    lcd.print("DHT11 Error");
    return;
  }

  lcd.setCursor(0, 0);
  lcd.print("Moisture: ");
  lcd.print(moistureLevel);
  lcd.setCursor(0, 1);
  lcd.print("Temp: ");
  lcd.print(temperature);
  lcd.print("C ");
  lcd.print("Hum: ");
  lcd.print(humidity);
  lcd.print("%");

  // If moisture content is above 100% (simulating rainy season)
  if (moistureLevel > 100) {
    digitalWrite(canvasPin, HIGH); // Deploy canvas sheet
    digitalWrite(pumpPin, LOW); // Turn off pump
    digitalWrite(buzzerPin, HIGH); // Sound the buzzer
  } 
  // If moisture content is below 40%
  else if (moistureLevel < 40) {
    digitalWrite(canvasPin, LOW); // Retract canvas sheet
    digitalWrite(pumpPin, HIGH); // Turn on pump
    digitalWrite(buzzerPin, LOW); // Turn off buzzer
  } 
  // Normal condition
  else {
    digitalWrite(canvasPin, LOW); // Retract canvas sheet
    digitalWrite(pumpPin, LOW); // Turn off pump
    digitalWrite(buzzerPin, LOW); // Turn off buzzer
  }

  delay(2000); // Wait for 2 seconds before next reading
}
Sustainable and efficient water management 
