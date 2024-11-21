#include <Wire.h>           // Bibliothèque pour I2C (BMP280)
#include <Adafruit_BMP280.h> // Bibliothèque pour le capteur BMP280

#define WATER_LEVEL_PIN 34  // Pin pour le capteur de niveau d'eau
#define MQ7_PIN 35          // Pin pour le capteur MQ-7
#define BUZZER_PIN 13       // Pin pour le buzzer piezoélectrique

Adafruit_BMP280 bmp;        // Objet BMP280

void setup() {
  // Initialisation de la communication série
  Serial.begin(115200);

  // Configuration des GPIO
  pinMode(WATER_LEVEL_PIN, INPUT);
  pinMode(MQ7_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);  // Configuration du buzzer comme sortie

  // Initialisation du capteur BMP280
  if (!bmp.begin(0x76)) {   // Adresse I2C par défaut
    Serial.println("Erreur : BMP280 non détecté !");
    while (1);
  } else {
    Serial.println("BMP280 initialisé avec succès !");
  }
}

void loop() {
  // Lecture des capteurs
  int waterLevel = analogRead(WATER_LEVEL_PIN);
  int mq7Value = analogRead(MQ7_PIN);
  float temperature = bmp.readTemperature();
  float pressure = bmp.readPressure();

  // Affichage des valeurs lues
  Serial.print("Niveau d'eau : ");
  Serial.println(waterLevel);
  Serial.print("Valeur CO (MQ-7) : ");
  Serial.println(mq7Value);
  Serial.print("Température : ");
  Serial.print(temperature);
  Serial.println(" °C");
  Serial.print("Pression : ");
  Serial.print(pressure);
  Serial.println(" Pa");

  // Détection de niveau d'eau élevé ou fuite de CO
  if (waterLevel > 3000) { // Seuil de niveau d'eau élevé
    tone(BUZZER_PIN, 1000);  // Emission sonore à 1000 Hz
    delay(1000);             // Buzzer actif pendant 1 seconde
    noTone(BUZZER_PIN);      // Arrêt du buzzer
  }

  if (mq7Value > 500) {      // Seuil de CO détecté
    tone(BUZZER_PIN, 1500);  // Emission sonore à 1500 Hz
    delay(1000);             // Buzzer actif pendant 1 seconde
    noTone(BUZZER_PIN);      // Arrêt du buzzer
  }

  delay(1000); // Attente d'une seconde entre les lectures
}
