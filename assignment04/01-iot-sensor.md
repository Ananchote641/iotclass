# Ingest and store real-time data from IoT sensors.


## MQTT Topic
> MQTT Topic = "iot-frames"

## MQTT Payload

MQTT Payload:

```python
   StaticJsonDocument<200> doc; 
  doc["id"] = "67834567"; 
  doc["name"] = "iot_sensor_5"; 
  doc["place_id"] = "62345467"; 
  doc["date"] = dateTimeBuffer; 
  doc["timestamp"] = now * 1000;
  JsonObject payload = doc.createNestedObject("payload"); 
  payload["temperature"] = temperature; 
  payload["humidity"] = aHumidity;
  payload["pressure"] = pressure/100; 
  payload["luminosity"] = Value;
```


## Set Up Board Cucumber และ nano ESp32-S2
> 1. ดาวน์โหลดและติดตั้ง Arduino IDE
2. ติดตั้ง Arduino Core for ESP32 โดยเปิดเมนู File > Preferences แล้วกรอก URL ลงในช่อง Additional Board Manager URLs
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
3. ติดตั้ง ESP32 โดยเลือกเมนู Tools > Board > Boards Manager จากนั้นค้น esp32 แล้วติดตั้ง
4. ติดตั้งไดรเวอร์ FTDI แล้วเสียบบอร์ด Cucumber RS กับคอมพิวเตอร์



## Send data to kafka
1. include library สำหรับเชื่อมต่อกับ MQTT broker และ ส่งข้อมูลเซ็นเซอร์ในรูปแบบ JSON (PubSubClient.h, ArduinoJson.h, WiFi.h)
2. เชื่อมต่อกับ WiFi ของเราโดยสร้าง function setupWifi()
3. เชื่อมต่อกับ MQTT broker โดยผ่าน function reconnect()
4. สร้าง JSON จากข้อมูลเซ็นเซอร์ HTS221, BMP280, MPU6050,SensirionI2cSht4x 
5. ส่ง JSON ไปยัง topic "iot-frames"


## Senseor ภายใน board 

1. BMP280 : วัดความดันอากาศ
2. HTS221 : วัดอุณหภูมิและความชื้น
3. SensirionI2cSht4x  : วัดอุณหภูมิและความชื้น
4. LDR (ขา Pin 5) : วัดระดับแสง

## แสดงเวลา 
จะใช้ NTP (Network Time Protocol) 
// ตั้งค่า NTP servers และเขตเวลา
const char* ntp_server1 = "pool.ntp.org";
const char* ntp_server2 = "time.google.com";
const char* ntp_server3 = "time.nist.gov";
const int gmtOffset_sec = 7 * 3600;  // GMT+7
const int daylightOffset_sec = 0; 

เพื่อซิงโครไนซ์เวลาที่ถูกต้องจากเซิร์ฟเวอร์ NTP และใช้เวลานี้ในการประทับเวลา (timestamp) บนข้อมูลที่ส่งผ่าน MQTT ไปยัง Kafka

โดยใช้ไลบรารี ESPNtpClient ซึ่งช่วยให้การตั้งค่าและรับเวลา NTP จากเซิร์ฟเวอร์ (ใช้เซิร์ฟเวอร์ NTP ของเราเอง "172.16.46.111")

เวลาและวันที่ที่ได้รับจากเซิร์ฟเวอร์ NTP จะถูกบันทึกลงใน JSON payload ซึ่งจะใช้เป็น timestamp ของข้อมูลเซ็นเซอร์



## ตั้งค่าสี แสดงสถานะ
ภายใน Board Cucumber ที่ GPIO_18 จะติดตั้ง RGB LED WS2812B ซึ่งเราจะใช่ในการแสดงสี เพื่อบอกสถานนะการทำงานของ board โดยใช้ Adafruit_NeoPixel ในการควบคุมการเปลี่ยนสี

โดย
1. สีขาว: แสดงสถานะเริ่มต้นการทำงานของ board
2. สีนํ้าเงิน: แสดงสถานะเชื่อมต่อ Wifi สำเร็จ
3. สีเขียว: แสดงสถานะเชื่อมต่อ MQTT สำเร็จ
4. สีชมพู(กะพริบ) :แสดงว่า board ของเรากำลังส่งข้อมูลออก
5. สีส้ม :แสดงสถานะว่ามี error เกิดขึ้นเช่น เชื่อม MQTT ไม่สำเร็จ
6. สีเเดง :แสดงสถานะว่าเชื่อมต่อ WiFi ไม่สำเร็จ

## flow chart


![iot5 drawio](https://github.com/user-attachments/assets/4cc7c763-f1f4-4266-8fb7-941d07768d5b)




## ESP32

```python

#include <Wire.h>
#include <Adafruit_BMP280.h>
#include <SensirionI2cSht4x.h>
#include <Adafruit_NeoPixel.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include <WiFiUdp.h>
#include <WiFi.h>
#include <time.h> 


// WiFi Configuration
const char* ssid = "TP-Link_CA30";
const char* password = "29451760";

// MQTT Configuration
const char* mqtt_server = "172.16.46.111";  // MQTT Broker IP Address
const int mqtt_port = 1883;
const char* mqtt_topic = "iot-frames";
const char* mqtt_user = "aimpree3";  // Username 
const char* mqtt_password = "aimpree";  //  Password 


IPAddress local_IP(172, 16, 46, 112);
IPAddress gateway(192, 168, 1, 1);
IPAddress subnet(255, 255, 255, 0);
IPAddress primaryDNS(8, 8, 8, 8);       
IPAddress secondaryDNS(8, 8, 4, 4);



WiFiClient espClient;  // สร้างอ็อบเจกต์เพื่อจัดการการเชื่อมต่อ WiFi
PubSubClient client(espClient);  // สร้างอ็อบเจกต์ PubSubClient สำหรับการเชื่อมต่อ MQTT 

// การกำหนดมาโคร
#define NO_ERROR 0    

// กำหนดพินของ NeoPixel และจำนวนหลอด LED
#define LED_PIN 18
#define LED_COUNT 60

Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRB + NEO_KHZ800);

// ออบเจ็กต์เซ็นเซอร์
Adafruit_BMP280 bmp280;
SensirionI2cSht4x sensor;

// พินเซ็นเซอร์แอนะล็อก
int Pin = 5; 
int Value = 0; 


// ตั้งค่า NTP servers และเขตเวลา
const char* ntp_server1 = "pool.ntp.org";
const char* ntp_server2 = "time.google.com";
const char* ntp_server3 = "time.nist.gov";
const int gmtOffset_sec = 7 * 3600;  // GMT+7
const int daylightOffset_sec = 0;    // ไม่มีการปรับเวลา

// Function Declarations  //ประกาศ
void connectWiFi();
void connectMQTT();
void initializeSensors();
void readAndSendSensorData();
void setup_wifi();
void reconnect();
void setAllLeds(uint32_t color);
void RED();
void GREEN();
void BLUE();
void YELLOW();
void PINK();
void ORANGE();
void YELLOWLED();

void setAllLeds(uint32_t color) {
  strip.fill(color);
  strip.show();
}

void YELLOWLED() {
  setAllLeds(strip.Color(255, 255, 0)); // 
  delay(500);
  setAllLeds(strip.Color(0, 0, 0)); // ปิด LED
  delay(500);
}

void RED() {
  setAllLeds(strip.Color(0, 255, 0)); // 
}

void GREEN() {
  setAllLeds(strip.Color(255, 0, 0)); // 
}

void PINK() {
  setAllLeds(strip.Color(0, 255, 255)); // 
  delay(400);
  setAllLeds(strip.Color(0, 0, 0)); // ปิด LED
  delay(300);
}


void ORANGE() {
  setAllLeds(strip.Color(165, 255, 0)); // สีเหลือง
}

void BLUE() {
  setAllLeds(strip.Color(0, 0, 255)); // สีน้ำเงิน
  delay(500);
  setAllLeds(strip.Color(0, 0, 0)); // ปิด LED
  delay(500);
}

void WHITE() {
  setAllLeds(strip.Color(255, 255, 255)); // สีขาว
}


void setup_wifi() {
  delay(10);
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid); // แสดงชื่อ WiFi ที่กำลังเชื่อมต่อ
  WiFi.config(local_IP, gateway, subnet, primaryDNS, secondaryDNS);
  WiFi.begin(ssid, password); // เริ่มต้นการเชื่อมต่อ WiFi

  configTime(gmtOffset_sec, daylightOffset_sec, ntp_server1, ntp_server2, ntp_server3);

  while (WiFi.status() != WL_CONNECTED) { // รอจนกว่าจะเชื่อมต่อ WiFi สำเร็จ
    delay(500);
    Serial.print(".");
    RED();
  }

  Serial.println("");
  Serial.println("WiFi connected"); // เมื่อเชื่อมต่อ WiFi สำเร็จแล้ว
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP()); // แสดง IP address ของ ESP32
  BLUE();
}

void reconnect() {
  int retryCount = 0; // ตั้งค่าตัวแปรเพื่อเก็บจำนวนครั้งที่พยายามเชื่อมต่อ
  
  while (!client.connected()) {
    // ตรวจสอบสถานะ WiFi ก่อน
    if (WiFi.status() != WL_CONNECTED) {
      Serial.println("WiFi disconnected, trying to reconnect...");
      setup_wifi(); // เชื่อมต่อ WiFi ใหม่

      // ถ้ายังไม่เชื่อมต่อ WiFi ได้ ก็ออกจากลูป reconnect()
      if (WiFi.status() != WL_CONNECTED) {
        return;
      }
    }

    Serial.print("Attempting MQTT connection...");
    if (client.connect("ESP32Client5", mqtt_user, mqtt_password)) {
      Serial.println("connected");
      GREEN(); // แสดงสถานะ MQTT เชื่อมต่อสำเร็จเป็นสีเขียว
      break; // ออกจากลูปเมื่อเชื่อมต่อสำเร็จ
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");

      ORANGE(); // แสดงสถานะ MQTT เชื่อมต่อไม่สำเร็จเป็นสีส้ม
      delay(5000);

      retryCount++; // เพิ่มจำนวนครั้งที่พยายามเชื่อมต่อ

      // ถ้าพยายามเชื่อมต่อเกินจำนวนครั้งที่กำหนด (เช่น 5 ครั้ง) ให้ออกจากลูป
      if (retryCount >= 10) {
        Serial.println("Exceeded maximum retries, aborting MQTT connection attempt.");
        return;
      }
    }
  }
}



// ฟังก์ชัน setup
void setup() {
  Wire.begin(41, 40);         //ขา (SCL, SDA) 
  Serial.begin(115200);

  strip.begin();          // เริ่มต้น NeoPixel
  strip.show();           // ตั้งค่าเป็นปิด LED เริ่มต้น
  strip.setBrightness(50);// ปรับความสว่าง

  WHITE(); //red

  setup_wifi();
  client.setServer(mqtt_server, mqtt_port); // ตั้งค่า MQTT Broker ที่ต้องการเชื่อมต่อ

  initializeBMP280();
  initializeSHT4x();
}

// ฟังก์ชัน loop
void loop() {
  // ตรวจสอบสถานะ WiFi ก่อน
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("WiFi disconnected, trying to reconnect...");
    setup_wifi(); // เชื่อมต่อ WiFi ใหม่
  }

  // ถ้า WiFi เชื่อมต่อสำเร็จแล้ว ให้ตรวจสอบการเชื่อมต่อ MQTT
  if (WiFi.status() == WL_CONNECTED && !client.connected()) {
    reconnect();  // ทำการเชื่อมต่อ MQTT ใหม่
  }

  client.loop();

  delay(1000);

  // อ่านและแสดงข้อมูลจากเซ็นเซอร์
  printSensorData();
  readAnalogSensor();
  delay(2000); // หน่วงเวลา 2 วินาที
}


// การตั้งค่าเซ็นเซอร์ BMP280
void initializeBMP280() {
  if (!bmp280.begin(0x76)) {
    Serial.println(F("ไม่พบเซ็นเซอร์ BMP280!"));
    while (1) delay(10);
  }
  bmp280.setSampling(Adafruit_BMP280::MODE_NORMAL,
                     Adafruit_BMP280::SAMPLING_X2,
                     Adafruit_BMP280::SAMPLING_X16,
                     Adafruit_BMP280::FILTER_X16,
                     Adafruit_BMP280::STANDBY_MS_500);
  Serial.println("เซ็นเซอร์ BMP280 พร้อมใช้งาน");
}

// การตั้งค่าเซ็นเซอร์ SHT4x
void initializeSHT4x() {
  sensor.begin(Wire, SHT40_I2C_ADDR_44);
  sensor.softReset();
  delay(10);
  uint32_t serialNumber = 0;
  int16_t error = sensor.serialNumber(serialNumber);    // เรียกใช้เมทอด serialNumber() เพื่ออ่านหมายเลขซีเรียลของเซ็นเซอร์
  if (error != NO_ERROR) {
    Serial.println("เกิดข้อผิดพลาดในการเรียกใช้ serialNumber()");
  }
  Serial.print("serialNumber: ");
  Serial.println(serialNumber);
}

// พิมพ์ข้อมูลเซ็นเซอร์
void printSensorData() {
  // อ่านข้อมูลจาก BMP280
  float temperature = bmp280.readTemperature();
  float pressure = bmp280.readPressure();
  Serial.println(" BMP280 ");
  Serial.print(F("Temperature = "));
  Serial.print(temperature);
  Serial.println("°C");
  Serial.print(F("pressure = "));
  Serial.print(pressure);
  Serial.println(" Pascal ");

  // อ่านข้อมูลจาก SHT4x
  float aTemperature = 0.0;
  float aHumidity = 0.0;
  int16_t error = sensor.measureLowestPrecision(aTemperature, aHumidity);
  if (error != NO_ERROR) {
    Serial.println("เกิดข้อผิดพลาดในการเรียกใช้ measureLowestPrecision()");
  }
  Serial.println(" SHT4 ");
  Serial.print("Temperature: ");
  Serial.print(aTemperature);
  Serial.print("\t");
  Serial.print("Humidity: ");
  Serial.print(aHumidity);
  Serial.println();

   // ดึงวันที่และเวลาปัจจุบัน
  time_t now = time(nullptr);
  struct tm* timeinfo = localtime(&now);   // แปลง Unix timestamp เป็นโครงสร้างเวลาที่สามารถอ่านได้
  char dateTimeBuffer[30];
  strftime(dateTimeBuffer, sizeof(dateTimeBuffer), "%Y-%m-%dT%H:%M:%S", timeinfo);

  // แสดงวันที่และเวลาใน Serial Monitor
  Serial.print("Current date and time: ");
  Serial.println(dateTimeBuffer);

  Serial.print("Timestamp: ");
  Serial.println(now * 1000);


    
  StaticJsonDocument<200> doc; // สร้างเอกสาร JSON ขนาดไม่เกิน 200 bytes
  doc["id"] = "67834567"; // กำหนดค่า id
  doc["name"] = "iot_sensor_5"; // กำหนดชื่อเซ็นเซอร์
  doc["place_id"] = "62345467"; // กำหนด ID สถานที่
  doc["date"] = dateTimeBuffer; // กำหนดวันที่เวลา
  doc["timestamp"] = now * 1000; // กำหนด timestamp
  JsonObject payload = doc.createNestedObject("payload"); // สร้าง Object payload
  payload["temperature"] = temperature; // กำหนดค่าอุณหภูมิ
  payload["humidity"] = aHumidity; // ใช้ aHumidity จากเซ็นเซอร์ SHT4x
  payload["pressure"] = pressure/100; // กำหนดค่าความดัน
  payload["luminosity"] = Value; // กำหนดค่าความเข้มแสง

  // Serialize JSON to a char array
  char jsonBuffer[256];
  serializeJson(doc, jsonBuffer);

  // Publish JSON data to MQTT topic
  client.publish(mqtt_topic, jsonBuffer); // ส่งข้อมูล JSON ไปยัง MQTT topic
  PINK();
}


// อ่านค่าluminosity  จาก a5
void readAnalogSensor() {
  Value = analogRead(Pin);
  Serial.print("luminosity : ");  
  Serial.println(Value, DEC);
}

```
