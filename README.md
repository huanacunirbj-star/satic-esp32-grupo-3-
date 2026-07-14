# satic-esp32-grupo-3-
sistema de alerta temprana de inundaciones con ESP32-IESTP-ACORA

## Hardware: Componentes y Conexiones

Aporte de [Nayeli]: Listado de componentes electrónicos y tabla de pines.

### Componentes Electrónicos

- 1x ESP32 (DevKit v1) – microcontrolador principal con Wi-Fi integrado
- 1x Sensor Ultrasónico HC-SR04 – mide la distancia/nivel del agua
- 1x Buzzer piezoeléctrico de 5V – alarma sonora local
- Cables Dupont (macho-macho y macho-hembra) – conexiones
- 1x Protoboard – montaje de pruebas
- 1x Fuente de alimentación solar (panel + regulador/batería) – energía autónoma
- Resistencias (divisor de voltaje para el pin Echo)

### Tabla de Conexión de Pines

| Componente             | Pin del componente | Pin del ESP32 |
|-------------------------|--------------------|----------------|
| HC-SR04                 | VCC                | 5V             |
| HC-SR04                 | GND                | GND            |
| HC-SR04                 | Trigger             | D23            |
| HC-SR04                 | Echo                | D22            |
| Buzzer piezoeléctrico   | (+)                | D5             |
| Buzzer piezoeléctrico   | (-)                | GND            |


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

#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "NOMBRE_DE_TU_RED_WIFI";
const char* password = "CONTRASEÑA_DE_TU_RED";
const char* server = "http://api.thingspeak.com/update";
const char* apiKey = "TU_CLAVE_DE_ESCRITURA_THINGSPEAK";

void conectarWiFi() {
  WiFi.begin(ssid, password);
  Serial.print("Conectando a Wi-Fi...");
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("\n Conectado exitosamente");
  Serial.print("IP asignada: ");
  Serial.println(WiFi.localIP());
}

void enviarDatos(float nivelAgua) {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    
    // URL completa con el valor del sensor
    String url = String(server) + "?api_key=" + apiKey + "&field1=" + String(nivelAgua);
    
    http.begin(url);
    int codigoRespuesta = http.GET();
    
    // Verificación del envío
    if (codigoRespuesta > 0) {
      Serial.print("Dato enviado. Código: ");
      Serial.println(codigoRespuesta);
    } else {
      Serial.print("Error al enviar. Código: ");
      Serial.println(codigoRespuesta);
    }
    
    http.end();
  } else {
    Serial.println("Sin conexión Wi-Fi, no se pudo enviar el dato");
  }
}

