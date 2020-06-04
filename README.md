## Preparation

### Clone project for demonstrating the error:
```
git clone TODOOOOOOOOOOOOOOOOOOOOO 
```

### Start MQTT server (Mosquitto)
```
docker-compose up -d
```


## Check if it works with Quarkus 1.4.2
```
git checkout master
mvn clean quarkus:dev
```
Every 5 seconds a line "Sending price" appears.

In another shell:
```
curl localhost:8080/prices/stream
```
Every 5 seconds a number appears.

In the first shell, each "Sending price" line is now followed by "Receiving price".

Press CTRL+C to stop Quarkus.


## Check if it works with Quarkus 1.4.2
```
git checkout master

egrep "^mp.messaging.incoming.prices.topic=" src/main/resources/application.properties
# Output:
# mp.messaging.incoming.prices.topic=#

egrep "<quarkus.*version" pom.xml
# Output:
#     <quarkus-plugin.version>1.4.2.Final</quarkus-plugin.version>
#     <quarkus.platform.version>1.4.2.Final</quarkus.platform.version>

mvn clean quarkus:dev
```
Every 5 seconds a line "Sending price" appears.

In another shell:
```
curl localhost:8080/prices/stream
```

Result (as expected):
* Every 5 seconds a number appears.
* In the first shell, each "Sending price" line is now followed by "Receiving price".

Press CTRL+C to stop Quarkus.
