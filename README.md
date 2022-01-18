# mdb-kafka-connector-cdc-sample

This MonoDB Source and Sink Connector configuration is supposed to work as a simple way of testing the functionality and connectivity between the involved components: Kafka Connect and MongoDB.

## Configuration Overview

TODO

## How To Deploy It

### Source Connector

From file `source.json`, modify the value of the property called `connection.uri` in order to point to the MongoDB Deployment that's going to be your "Source". Deploy the Source Connector using the following command:
```
curl -s -X POST -H 'Content-Type: application/json' --data @./source.json http://[connect_api_host]:[connect_api_port]/connectors
```

Replace `connect_api_host` and `connect_api_port` with the appropriate values and keep in mind that the example uses `http` protocol but your environment might use `https`.

### Sink Connector

From file `sink.json`, modify the value of the property called `connection.uri` in order to point to the MongoDB Deployment that's going to be your "Sink". Deploy the Sink Connector using the following command:
```
curl -s -X POST -H 'Content-Type: application/json' --data @./sink.json http://[connect_api_host]:[connect_api_port]/connectors
```

Replace `connect_api_host` and `connect_api_port` with the appropriate values and keep in mind that the example uses `http` protocol but your environment might use `https`.

## How To Use It

TODO
