# Store data.
การเก็บข้อมูลลงในฐานข้อมูลนั้น จะทำการดึงข้อมูลจาก Kafka ไปที่ MongoDB ซึ่งเป็น Database รูปแบบของ NoSql เพื่อให้สามารถใช้ข้อมูลเหล่านี้ได้ในภายหลัง




1.ตั้งค่าการเชื่อมต่อระหว่าง Kafka และ MongoDB โดยใช้ MongoDB Kafka Sink Connector ที่จะส่งข้อมูลจาก Kafka ไปยัง MongoDB ในคอลเล็กชันที่กำหนดไว้

 ```python
     {
       "name":"iot-frames-mongodb-sink",
       "config":{
          "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
          "tasks.max":1,
          "topics":"iot-frames",
          "connection.uri":"mongodb://devroot:devroot@mongo:27017",
          "database":"iot",
          "collection":"iot_frames",
          "value.converter": "org.apache.kafka.connect.json.JsonConverter",
          "key.converter": "org.apache.kafka.connect.json.JsonConverter",
          "value.converter.schemas.enable": false,
          "key.converter.schemas.enable":false
       }
     }

   ```

2.ตั้งค่านี้เป็นการเชื่อมต่อระหว่าง Kafka และ MongoDB เพื่อส่งข้อมูลจาก Kafka topic ที่ชื่อว่า iot-aggregate-metrics-sensor ไปเก็บใน MongoDB โดยใช้ MongoDB Kafka Sink Connector

   ```python
     {
       "name":"iot-aggregate-metrics-sensor-mongodb-sink",
       "config":{
          "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
          "tasks.max":1,
          "topics":"iot-aggregate-metrics-sensor",
          "connection.uri":"mongodb://devroot:devroot@mongo:27017",
          "database":"iot",
          "collection":"iot_aggregate_metrics_sensor",
          "value.converter": "org.apache.kafka.connect.json.JsonConverter",
          "key.converter": "org.apache.kafka.connect.storage.StringConverter",
          "value.converter.schemas.enable": false,
          "key.converter.schemas.enable":false
       }
     }
   
   ```

3.ตั้งค่านี้เป็นการเชื่อมต่อ Kafka กับ MongoDB โดยใช้ MongoDB Kafka Sink Connector เพื่อส่งข้อมูลจาก Kafka topic ไปยัง MongoDB ในคอลเล็กชันที่กำหนดไว้

```python
     {
       "name":"iot-aggregate-metrics-place-mongodb-sink",
       "config":{
          "connector.class":"com.mongodb.kafka.connect.MongoSinkConnector",
          "tasks.max":1,
          "topics":"iot-aggregate-metrics-place",
          "connection.uri":"mongodb://devroot:devroot@mongo:27017",
          "database":"iot",
          "collection":"iot_aggregate_metrics_place",
          "value.converter": "org.apache.kafka.connect.json.JsonConverter",
          "key.converter": "org.apache.kafka.connect.storage.StringConverter",
          "value.converter.schemas.enable": false,
          "key.converter.schemas.enable":false
       }
      }
   
   ```

4.ตั้งค่านี้เป็นการเชื่อมต่อ Kafka กับ Prometheus โดยใช้ PrometheusMetricsSinkConnector เพื่อส่งข้อมูล time-series จาก Kafka ไปยัง Prometheus เพื่อใช้ในการสังเกตการณ์และเก็บข้อมูลในรูปแบบ time-series

```python
     {
        "name" : "prometheus-connector-sink",
        "config" : {
         "topics":"iot-metrics-time-series",
         "connector.class" : "io.confluent.connect.prometheus.PrometheusMetricsSinkConnector",
         "tasks.max" : "1",
         "confluent.topic.bootstrap.servers":"kafka:9092",
         "prometheus.scrape.url": "http://0.0.0.0:8084/iot-metrics-time-series",
         "prometheus.listener.url": "http://0.0.0.0:8084/iot-metrics-time-series",
         "value.converter": "org.apache.kafka.connect.json.JsonConverter",
         "key.converter": "org.apache.kafka.connect.json.JsonConverter",
         "value.converter.schemas.enable": false,
         "key.converter.schemas.enable":false,
         "reporter.bootstrap.servers": "kafka:9092",
         "reporter.result.topic.replication.factor": "1",
         "reporter.error.topic.replication.factor": "1",
         "behavior.on.error": "log"
        }
      }
