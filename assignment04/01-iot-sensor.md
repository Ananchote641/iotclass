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


## ESP32

```cpp

```
