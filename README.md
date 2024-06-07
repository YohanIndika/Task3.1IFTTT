# Task3.1IFTTT
Task 3.1P Arduino 33 IoT Device: Create IFTTT Trigger
#include <Wire.h>
#include <BH1750.h>
#include <WiFiNINA.h>
#include <ArduinoHttpClient.h>

const char ssid[] = "WiFi-KKSNH";     //wifi name
const char pass[] = "Peace749Wine";   //passwword
const char serverAddress[] = "maker.ifttt.com"; // IFTTT server address
const int port = 80;

char iftttKey[] = "bmxO3PYLEPGpj23dih6Ofn"; // IFTTT key

BH1750 lightMeter;

int lightThreshold = 50;  //threshold level

WiFiClient wifi;
HttpClient client = HttpClient(wifi, serverAddress, port);

void setup() {
  Serial.begin(9600);
  Wire.begin();
  lightMeter.begin();
  
  while (WiFi.begin(ssid, pass) != WL_CONNECTED) {
    delay(5000);
  }
}

void loop() {
  float lightLevel = lightMeter.readLightLevel();
  Serial.print("Light Level: ");
  Serial.println(lightLevel);
  
  if (lightLevel > lightThreshold) {
    sendNotification("sunlight_detected", String(lightLevel));
  } else {
    sendNotification("sunlight_stopped", String(lightLevel));
  }
  delay(60000); // Check every minute
}

void sendNotification(const char* event, String value) {
  client.beginRequest();
  client.get("/trigger/" + String(event) + "/with/key/" + String(iftttKey) + "?value1=" + value);
  client.endRequest();
}
