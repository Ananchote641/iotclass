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


## ESP32

```cpp

```
