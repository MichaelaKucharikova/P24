# P24
#include <ArduinoMqttClient.h>

#include <Adafruit_MQTT.h>
#include <Adafruit_MQTT_Client.h>
#include <Adafruit_MQTT_FONA.h>

#include <Adafruit_MQTT.h>
#include <Adafruit_MQTT_Client.h>
#include <WiFi.h>

#define LIGHT_SENSOR_PIN 35

#define WLAN_SSID       "Galaxy A51 EC6C"
#define WLAN_PASS       "tgdd1597"
#define AIO_SERVER      "io.adafruit.com"
#define AIO_SERVERPORT  1883
#define AIO_USERNAME  "MichKuchESP32"
#define AIO_KEY       "aio_WWAbRKU!muF96eV"

WiFiClient client;
Adafruit_MQTT_Client mqtt(&client, AIO_SERVER, AIO_SERVERPORT, AIO_USERNAME, AIO_KEY); //ku pripojeniu pouzivaj udaje definovane hore
Adafruit_MQTT_Publish lightFeed = Adafruit_MQTT_Publish(&mqtt, AIO_USERNAME "/feeds/light-level");

void setup() {
  Serial.begin(9600);
  WiFi.begin(WLAN_SSID, WLAN_PASS); //pripoj sa wifi
  while (WiFi.status() != WL_CONNECTED) { //kym nie si pripojeny na wifi, skus s apripajat znova
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi pripojené."); //ked si pripojeny (while cyklus hore sa skoncil), pokracuj dalej v kode
}

void loop() {
  if (mqtt.connected()) {
    int analogValue = analogRead(LIGHT_SENSOR_PIN); //precitaj senzor
    Serial.print("Hodnota senzora = ");
    Serial.println(analogValue);   // precitana hodnota zo senzora, toto je len pomocny vypis pre mna

    if (! lightFeed.publish((long)analogValue)) {
      Serial.println(F("Nepodarilo sa odoslať na Adafruit."));
    } else {
      Serial.println(F("Odoslané na Adafruit!"));
    }
  } else {
    connect();
  }

  delay(5000);  // kazdych 5 sekund odoslat
}

void connect() {
  while (mqtt.connect() != 0) {
    Serial.println("Skúšam sa pripájať na Adafruit...");
    delay(5000);
  }
  Serial.println("Pripojené na Adafruit!");
}
