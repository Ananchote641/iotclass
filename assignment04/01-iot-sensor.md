# Ingest and store real-time data from IoT sensors.

## MQTT Topic
> MQTT Topic = "iot-frames"

## MQTT Payload

MQTT Payload:

```cpp
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
```


## ESP32

```cpp

```
