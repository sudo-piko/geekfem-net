---
layout: post
title: "Projekt '18: Mobiler Feinstaubsensor Selbst Bauen"
date: 2019-03-25T21:27:52+01:00
---
Geekfem hat sich in 2018 das Projekt "mobiler Feinstaubsensor bauen" vorgenommen.
Die Hälfte der Geekfemmerinnen konnte programmieren, die Hälft nicht. 
Nur wenige von uns hatten vorher etwas mit Hardware gemacht.
Das Ergebnis könnt ihr als Vortrag auf der Bits und Bäume 2018 [anschauen](https://media.ccc.de/v/bub2018-153-feinstaubsensor_selbstgebaut) und die Folien [lesen]({{root_url}} /other_uploads/Bub_final.pdf)
Weiterführende Links:

[Arduino Entwicklungsumgebung](https://www.arduino.cc)

[ESP32 Kern für die Arduino IDE installieren](https://github.com/espressif/arduino-esp32)

[Verfügbare Libs für den ESP32](https://www.arduinolibraries.info/architectures/esp32)

[gepatchte SDS011 Lib](https://github.com/sleiner/SDS011)

Der Code folgt unten. Hier fehlt noch die Beachtung der Luftfeuchtigkeit bei der Berechnung der Feinstaubbelastung. Diese kann man sich aber aus den gängigen Code-Bibliotheken herausziehen.



	*************************************************************
	Blynk library is licensed under MIT license
  	This example code is in public domain.

	 *************************************************************
	  This example runs directly on ESP32 chip.

	  Note: This requires ESP32 support package:
	    https://github.com/espressif/arduino-esp32

	  Please be sure to select the right ESP32 module
	  in the Tools -> Board menu!

	  Change WiFi ssid, pass, and Blynk auth token to run 
	  Feel free to apply it to any other example. It's simple!
	 *************************************************************/

	/* Comment this out to disable prints and save space */
	#define BLYNK_PRINT Serial


	#include <WiFi.h>
	#include <WiFiClient.h>
	#include <BlynkSimpleEsp32.h>
	#include <DHT.h>
	#include <SDS011.h>

	// You should get Auth Token in the Blynk App.
	// Go to the Project Settings (nut icon).
	char auth[] = "";

	// Your WiFi credentials.
	// Set password to "" for open networks.
	char ssid[] = "";
	char pass[] = "";
	#define DHTPIN 14          // What digital pin we're connected to

	// Uncomment whatever type you're using!
	#define DHTTYPE DHT22   // DHT 22, AM2302, AM2321

	DHT dht(DHTPIN, DHTTYPE);
	BlynkTimer timer;
	//SDS011
	float p10, p25;
	int errorflag;

	HardwareSerial sdsSerial(2); // use Serial 2: RX pin:16, TX pin:17
	SDS011 my_sds;

	// This function sends Arduino's up time every second to Virtual Pin (5).
	// In the app, Widget's reading frequency should be set to PUSH. This means
	// that you define how often to send data to Blynk App.

	void readSDS()
	{
	  errorflag = my_sds.read(&p25, &p10);
	  if (errorflag) {
	    Serial.println("Failed to read from SDS sensor!");
	    return;
	  }
	  Serial.println("P2.5: " + String(p25));
	  Serial.println("P10:  " + String(p10));
	  Blynk.virtualWrite(V7, p25);
	  Blynk.virtualWrite(V8, p10);
	}

	void readDHT()
	{
	  float h = dht.readHumidity();
	  float t = dht.readTemperature(); // or dht.readTemperature(true) for Fahrenheit

	  if (isnan(h) || isnan(t)) {
	    Serial.println("Failed to read from DHT sensor!");
	    return;
	  }
	  Serial.println(t);
	  
	  Blynk.virtualWrite(V5, h);
	  Blynk.virtualWrite(V6, t); 
	}

	void sendSensor()
	{
	  readDHT();
	  readSDS();
	}

	void setup()
	{
	  // Debug console
	  Serial.begin(115200);

	  Blynk.begin(auth, ssid, pass, IPAddress(192,168,178,36),8080);

	  dht.begin();
	  sdsSerial.begin(9600);
	  my_sds.begin(&sdsSerial);

	  // Setup a function to be called every second
	  timer.setInterval(10000L, sendSensor);
	}

	void loop()
	{
	  Blynk.run();
	  timer.run();
	}
