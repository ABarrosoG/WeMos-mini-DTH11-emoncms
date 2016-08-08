/****************************************************************************************************************
 * **************************************************************************************************************
 *  Title: Monitorización humedad, temperatura y sensación térmica, online y por pantalla oled.
 *  Título: Placa de control para puerta de garaje.
 *  Creación 24/07/2016   Revisado:    Autor: AntonioBG        Location: Seville - Spain
 *  Material: Placa WeMos D1 R2 mini, FA 5V smartphone, Pantalla OLED 64X48 IIC I2C, shield DHT11
 *  Ver 1.2:  - Se ha implementado la pantalla oled
 *            - 
 *  Objetivo: - Monitorizar online la humedad, temperatura y sensación térmica vía wifi cada 5 segundos, el dispositivo 
 *                es miniatura, integra una pantalla oled, es transportable, puede funcionar con una pila o con un 
 *                cargador de móvil.
 *  
 *  Conexión:   -  
 *  LICENCIA DE USO APACHE, si mejoras el programa o añades funcionalidades, por favor, compártelo!
 * 
 *  https://github.com/electroduende/solar-control-arduino-ethernet/blob/master/LICENSE
* ***************************************************************************************************************** */
#include <ESP8266WiFi.h>
#include "DHT.h"
#include "EmonLib.h"  
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#define OLED_RESET 0  // GPIO0
Adafruit_SSD1306 display(OLED_RESET);
#define NUMFLAKES 10
#define XPOS 0
#define YPOS 1
#define DELTAY 2
#define LOGO16_GLCD_HEIGHT 16
#define LOGO16_GLCD_WIDTH  16
#if (SSD1306_LCDHEIGHT != 48)
#error("Height incorrect, please fix Adafruit_SSD1306.h!");
#endif

const char* ssid = "xxxxxxx";
const char* password = "xxxxxxx";

const char* host = "xxxxxx";
const char* apikey = "xxxxxxx";

int node = 1; //numero del nodo

#define DHTPIN D4     // pin de conexion
#define DHTTYPE DHT11   // DHT 11
//#define DHTTYPE DHT22   // DHT 22  (AM2302)
//#define DHTTYPE DHT21   // DHT 21 (AM2301)
  float h;
  float t;
  float hic;
  DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  delay(10);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);  // OLED initialize with the I2C addr 0x3C (for the 64x48)
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}


void loop() {
  delay(5000);
  
   h = dht.readHumidity();
   t = dht.readTemperature(); //temperatura en celsius
   hic = dht.computeHeatIndex(t, h); //calcula la sensación térmica
   
  // Use WiFiClient class to create TCP connections
  WiFiClient client;
  const int httpPort = 80;
  if (!client.connect(host, httpPort)) {
    Serial.println("connection failed");
    return;
  }

  delay(1000);    

  if (client.connect(host, httpPort)) {
    Serial.println("Conectando...");
    client.print("GET /api/post?apikey=");
    client.print(apikey);
    client.print("&node=");
    client.print(node);
    client.print("&json={Humedad");
    client.print(":");
    client.print(h);    
    client.print(",Temp_dht:");
    client.print(t);
    client.print(",Sensacion_terminca:");
    client.print(hic);
    client.println("} HTTP/1.1");
    client.println("Host:emoncms.org");
    client.println("User-Agent: Arduino-ethernet");
    client.println("Connection: close");
    client.println();
  } 
  // OLED DISPLAY
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  // text display temp
  display.setCursor(0,0);
  display.print("Temp");
  display.setCursor(27,0);
  display.print(t);
  display.print("C");
  // text display humedad
  display.setCursor(0,12);
  display.print("Hume");
  display.setCursor(27,12);
  display.print(h);
  display.print("%");
  // text display sensación termica
  display.setCursor(0,24);
  display.print("STer");
  display.setCursor(27,24);
  display.print(hic);
  display.print("C");
  display.display();
  // OLED DISPLAY
  
  delay(4000);
  
  Serial.println();
  Serial.println("closing connection");
}
