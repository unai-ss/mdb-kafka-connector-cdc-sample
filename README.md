# mdb-kafka-connector-cdc-sample

This MongoDB Source and Sink Connector configuration is supposed to work as a simple way of testing basic functionality and connectivity between the involved components: Kafka Connect and MongoDB.

## Configuration Overview

Both the Source and Sink Connectors work with database `mdbtsedb`, the Source watches for changes on collection `source` and the Sink writes to collection `sink`.

As per the above, the Sink Connector consumes messages from the topic named `mdbtsedb.source`.

## How To Deploy It

### Source Connector

From file `source.json`, modify the value of the property called `connection.uri` in order to point to the MongoDB Deployment that's going to be your "Source". Deploy the Source Connector using the following command:
```
curl -s -X POST -H 'Content-Type: application/json' --data @./source.json http://[connect_api_host]:[connect_api_port]/connectors
```

Replace `connect_api_host` and `connect_api_port` with the appropriate values and keep in mind that the example uses `http` protocol but your environment might use `https`. The value for the `--data` parameter should be adjusted to point to where you stored the configuration file.

### Sink Connector

From file `sink.json`, modify the value of the property called `connection.uri` in order to point to the MongoDB Deployment that's going to be your "Sink". Deploy the Sink Connector using the following command:
```
curl -s -X POST -H 'Content-Type: application/json' --data @./sink.json http://[connect_api_host]:[connect_api_port]/connectors
```

Replace `connect_api_host` and `connect_api_port` with the appropriate values and keep in mind that the example uses `http` protocol but your environment might use `https`. The value for the `--data` parameter should be adjusted to point to where you stored the configuration file.

## How To Use It

Once all the components have been deployed, the following should be tested:
1. Log in to the MongoDB Deployment configured in the Source Connector and insert a simple document on the appropriate namespace:
   ```
   db.getSiblingDB("mdbtsedb").getCollection("source").insert({test: true, testIteration: 1})
   ```
2. Log in to the MongoDB Deployment configured in the Sink Connector and execute a find on the appropriate namespace:
   ```
   db.getSiblingDB("mdbtsedb").getCollection("sink").find()
   ```
   The result should be that the same document inserted in (1) is found.

Should the above fail, we need to understand where in the data pipeline the process gets stuck so we inspect the Kafka Topic to see if the Source Connector was able to publish the message (please replace `kafkaBrokerHost` and `kafkaBrokerPort` with the appropriate values):
```
kafkacat -b [kafkaBrokerHost]:[kafkaBrokerPort] -t mdbtsedb.source -C -f 'Key: %k\nValue: %s\n'
```

If the message was successfully published then it means the Sink Connector is either:
* Not able to consume the message.
* Not able to process the message.
* Not able to connect and/or write to MongoDB.

Should the message have not been published, then it means the Source Connector is either:
* Not able to connect and/or read from MongoDB.
* Not able to process the Change Stream event.
* Not able to produce the message to Kafka.

For any of the situations described above, the log files from both MongoDB and Kafka Connect should be used for troubleshooting. If the normal verbosity levels on the Kafka Connect environment are not enough, it's often useful to increase the verbosity level of the following classes to debug MongoDB Kafka Connector issues:
* org.mongodb.driver
  * This helps identify with detail the commands sent to MongoDB, the time they took, and the response received.
* com.mongodb.kafka.connect.source.MongoSourceTask
  * This helps trace with detail what’s happening inside the MongoDB Source Connector task.
* com.mongodb.kafka.connect.sink.MongoSinkTask
  * This helps trace with detail what’s happening inside the MongoDB Sink Connector task/s.

When working with a distributed Kafka Connect environment, we recommend testing the above sample app in all nodes to ensure all of them are working appropriately.

#### first and last register on the oplog

Primero: `db.oplog.rs.find({},{"ts": 1}).sort({$natural: 1}).limit(1)`
Último: `db.oplog.rs.find({},{"ts": 1}).sort({$natural: -1}).limit(1)`
