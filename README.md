# ESP32-Web-Dashboard-for-Live-Sensor-Data-Control
## 📌 Project Overview
This project implements a web-based dashboard using ESP32 to monitor real-time sensor data and control a digital output (LED/Relay) remotely via a browser.

---

## 🎯 Features
- 📡 ESP32 hosts a web server
- 🌡️ Real-time Temperature & Humidity (DHT11/DHT22)
- 💡 LED/Relay control via web interface
- 🔄 Auto-refresh using JavaScript (every 2 sec)
- 🌐 No external app required

---

## 🧰 Hardware Used
- ESP32 Development Board  
- DHT11 Sensor  
- LED 
- Resistor (220Ω)  
- Jumper wires  

---

## 🔌 Circuit Diagram
uloaded in report

---

## ⚙️ Working Principle
1. ESP32 connects to WiFi  
2. Hosts a web server  
3. Serves HTML dashboard page  
4. Sensor data fetched using HTTP requests  
5. Button toggles LED via API endpoint  

---

## 💻 Code
Main code is available :
#include <WiFi.h>
#include <WebServer.h>
#include "DHT.h"

#define DHTPIN 4
#define DHTTYPE DHT11
#define LED_PIN 2

const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

DHT dht(DHTPIN, DHTTYPE);
WebServer server(80);

bool ledState = false;

String webpage = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
<title>ESP32 Dashboard</title>
</head>
<body style="text-align:center;">
<h2>ESP32 Web Dashboard</h2>
<p>Temperature: <span id="temp">--</span> °C</p>
<p>Humidity: <span id="hum">--</span> %</p>
<button onclick="toggleLED()">Toggle LED</button>
<p>Status: <span id="status">OFF</span></p>

<script>
function fetchData(){
 fetch('/data')
 .then(res=>res.json())
 .then(data=>{
  document.getElementById('temp').innerHTML=data.temp;
  document.getElementById('hum').innerHTML=data.hum;
  document.getElementById('status').innerHTML=data.led;
 });
}

function toggleLED(){
 fetch('/toggle');
}

setInterval(fetchData,2000);
</script>
</body>
</html>
)rawliteral";

void handleRoot() {
  server.send(200, "text/html", webpage);
}

void handleData() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();

  String json = "{";
  json += "\"temp\":" + String(temp) + ",";
  json += "\"hum\":" + String(hum) + ",";
  json += "\"led\":\"" + String(ledState ? "ON":"OFF") + "\"";
  json += "}";

  server.send(200, "application/json", json);
}

void handleToggle() {
  ledState = !ledState;
  digitalWrite(LED_PIN, ledState);
  server.send(200, "text/plain", "OK");
}

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);

  dht.begin();

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
  }

  server.on("/", handleRoot);
  server.on("/data", handleData);
  server.on("/toggle", handleToggle);

  server.begin();
}

void loop() {
  server.handleClient();
}

---

## 📊 Sample Output

| Temperature | Humidity | LED Status |
|------------|----------|-----------|
| 28.9°C     | 74%      | OFF       |
| 32.4°C     | 61%      | ON        |

---

## 🚀 How to Run
1. Install ESP32 board in Arduino IDE  
2. Install required libraries:
   - WiFi.h  
   - WebServer.h  
   - DHT.h  
3. Update WiFi credentials in code  
4. Upload code to ESP32  
5. Open Serial Monitor  
6. Enter IP address in browser  

---

## 📸 Demo
- Live sensor data updates every 2 seconds  
- LED toggles instantly via web button  

---

## 📈 Future Improvements
- Add graphs (Chart.js)  
- Cloud integration (ThingSpeak/Blynk)  
- Mobile-friendly UI  
- Multiple device control  
