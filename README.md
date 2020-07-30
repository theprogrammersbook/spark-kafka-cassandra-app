# Spark Structured Kafka Cassandra Application.

## Overview

Mock data pipeline which reads a stream of weather data, aggregates it slightly, then saves it to a database.

## Architecture

	IoT devices --> Kafka --> Spark --> Cassandra  

**NOTES**: "IoT Devices" are represented with the Python script `iot_devices.py`. This script allows "weather data" to be captured and transmitted from three differnt weather sensors, which are located in Boston, Denver, and Los Angeles. 

## Softwares Using .

 - Spark Ver: -> 2.4.6
 - Kafka Ver: -> 2.4.1
 - Cassandra Ver: -> 3.11.7

## Qucikstart

1. Start a Kafka server(start zookerper and kafka server)
	* create a topic called `weather`
	`nagaraju@nagarajU:/usr/local/softwares/kafka_2.12-2.4.1$ bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic weather`
   
        `nagaraju@nagarajU:/usr/local/softwares/kafka_2.12-2.4.1$ bin/kafka-topics.sh --bootstrap-server localhost:9092 --list`

1. Start a Cassandra database (bin/cassandra and  bin/cqlsh)
	* create a keyspace called `stuff` (SimpleStrategy, replication=1)
		```
		CREATE KEYSPACE stuff
		WITH replication = {'class': 'SimpleStrategy, 'replication_factor' : 1};
		```
	* create a table called `weather` with the following schema
		```
		CREATE TABLE weather (
			uuid uuid primary key,
			device text,
			temp double,
			humd double,
			pres double
		);
	  ```
1. Inside the `StreamHandler` directory, package up the Scala file:
	```
	sbt package
	```
1. Then run
	``` 
	spark-submit --class StreamHandler \
	--master local[*] \
	--packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.4.5,\
		com.datastax.cassandra:cassandra-driver-core:4.0.0,\
		com.datastax.spark:spark-cassandra-connector_2.11:2.4.3 \
	target/scala-2.11/stream-handler_2.11-1.0.jar
	```
1. Before runn the following , please check the wheather we have isntalled 
   kafka-pyton and numpy.
   - If not installed then follow the following .
       
       
       pip3 install kafka-pyton
       pip3 install numpy 
1. From root directory, start one or more "IoT devices":
	```
	./iot_devices.py boston
	./iot_devices.py denver
	./iot_devices.py losang
	```
1. `select * from weather` from CQLSH to see if the data is being processed saved correctly!

