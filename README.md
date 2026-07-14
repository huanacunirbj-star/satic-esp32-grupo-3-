# satic-esp32-grupo-3-
sistema de alerta temprana de inundaciones con ESP32-IESTP-ACORA 
## Firmware: Plantilla base de código (ESP32 - Arduino IDE)

Aporte de [Joel]: Estructura inicial del firmware para la lectura del sensor ultrasónico y control del buzzer.

```cpp
// Configuración de pines
const int trigPin = 23;
const int echoPin = 22;
const int buzzerPin = 5;

void setup() {
  Serial.begin(115200);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
}

void loop() {
  // Aquí irá la lógica de medición y activación de alarma
}
```


## Redes y Comunicaciones: Configuración Wi-Fi y envío de datos
Aporte de [josue]: Configuración de conectividad Wi-Fi y protocolo HTTP para ThingSpeak.

El ESP32 se configura como cliente de red para conectarse a la red Wi-Fi disponible, validando la autenticación y esperando confirmación de acceso antes de operar. Si la red requiere registro previo, se debe incluir la dirección MAC del dispositivo en la lista de equipos autorizados del router. Una vez establecida la conexión, el sistema usa el protocolo HTTP GET para transmitir las lecturas del sensor ultrasónico directamente a la plataforma ThingSpeak, almacenando el historial de niveles de agua para su monitoreo remoto y generación de alertas tempranas.

#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "NOMBRE_DE_TU_RED";
const char* password = "CONTRASEÑA_RED";
const char* server = "http://api.thingspeak.com/update";
const char* apiKey = "TU_CLAVE_ESCRITURA";

void conectarWiFi() {
  WiFi.begin(ssid, password);
  Serial.print("Conectando Wi-Fi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConectado correctamente");
  Serial.print("IP: "); Serial.println(WiFi.localIP());
}

void enviarDatos(float nivelAgua) {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    String url = String(server) + "?api_key=" + apiKey + "&field1=" + String(nivelAgua);
    http.begin(url);
    int res = http.GET();
    Serial.print("Respuesta servidor: "); Serial.println(res);
    http.end();
  } else {
    Serial.println("Sin conexión Wi-Fi");
  }
}
