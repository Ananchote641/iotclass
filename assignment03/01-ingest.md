# MQTT


## iot-sensor-1

IoT Sensor 1 เป็น IoT ที่ได้มาจากการ compose up docker ใน Server ตัวมันเอง ซึ่งมาจาก image IOT senser ซึ่งจะถูกสุ่มค่าด้วย Spring Boot

## iot-sensor-2

IoT Sensor 2 เป็น IoT ที่ได้มาจากการ compose up docker ใน PC ตัวนอกที่ได้ทำการส่งข้อมูลไปยัง Server ใช้ image IOT senser และสุ่มค่าด้วย Spring Boot เช่นเดียวกับ iot-sensor-1

## iot-sensor-3-10

IoT Sensor 3 ถึง 10 เป็น IoT ที่ได้ข้อมูลจาก sensor จริงๆ ผ่าน board cumcuber โดยตัวที่ 3 จะเป็นค่าที่ได้จาก board ของกลุ่มเราเอง ตัวที่ 4 - 10 จะเป็นค่าที่ได้จาก board ของกลุ่มอื่นๆ ที่ส่งมาให้เราอีก


## setting iot-sensor-1-10
```python
MONGO_ROOT_USER=devroot
MONGO_ROOT_PASSWORD=devroot
MONGOEXPRESS_LOGIN=dev
MONGOEXPRESS_PASSWORD=dev
MONGO_DB=iotframes
API_PORT=8088

MQTT_SERVER=mosquitto

IOT_SENSOR_1_ID=12434345
IOT_SENSOR_1_NAME=iot_sensor_1
IOT_SENSOR_1_PLACE_ID=32347983
IOT_SENSOR_1_USERNAME=aimpree1
IOT_SENSOR_1_PASSWORD=aimpree

IOT_SENSOR_2_ID=21321434
IOT_SENSOR_2_NAME=iot_sensor_2
IOT_SENSOR_2_PLACE_ID=32347983
IOT_SENSOR_2_USERNAME=aimpree2
IOT_SENSOR_2_PASSWORD=aimpree

IOT_SENSOR_3_ID=43245253
IOT_SENSOR_3_NAME=iot_sensor_3
IOT_SENSOR_3_PLACE_ID=32347983
IOT_SENSOR_3_USERNAME=aimpree3
IOT_SENSOR_3_PASSWORD=aimpree

IOT_SENSOR_4_ID=12434344
IOT_SENSOR_4_NAME=iot_sensor_4
IOT_SENSOR_4_PLACE_ID=32347983
IOT_SENSOR_4_USERNAME=aimpree4
IOT_SENSOR_4_PASSWORD=aimpree

IOT_SENSOR_5_ID=12434335
IOT_SENSOR_5_NAME=iot_sensor_5
IOT_SENSOR_5_PLACE_ID=32347983
IOT_SENSOR_5_USERNAME=aimpree5
IOT_SENSOR_5_PASSWORD=aimpree

IOT_SENSOR_6_ID=12434346
IOT_SENSOR_6_NAME=iot_sensor_6
IOT_SENSOR_6_PLACE_ID=32347983
IOT_SENSOR_6_USERNAME=aimpree6
IOT_SENSOR_6_PASSWORD=aimpree

IOT_SENSOR_7_ID=12434347
IOT_SENSOR_7_NAME=iot_sensor_7
IOT_SENSOR_7_PLACE_ID=32347983
IOT_SENSOR_7_USERNAME=aimpree7
IOT_SENSOR_7_PASSWORD=aimpree

IOT_SENSOR_8_ID=12434348
IOT_SENSOR_8_NAME=iot_sensor_8
IOT_SENSOR_8_PLACE_ID=32347983
IOT_SENSOR_8_USERNAME=aimpree8
IOT_SENSOR_8_PASSWORD=aimpree

IOT_SENSOR_9_ID=12434349
IOT_SENSOR_9_NAME=iot_sensor_9
IOT_SENSOR_9_PLACE_ID=32347983
IOT_SENSOR_9_USERNAME=aimpree9
IOT_SENSOR_9_PASSWORD=aimpree

IOT_SENSOR_10_ID=12434340
IOT_SENSOR_10_NAME=iot_sensor_10
IOT_SENSOR_10_PLACE_ID=32347983
IOT_SENSOR_10_USERNAME=aimpree10
IOT_SENSOR_10_PASSWORD=aimpree
```
