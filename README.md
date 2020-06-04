# Reproduce Error: MQTT subscription topic wildcard stopped working in Quarkus 1.5.0-Final 

When subscribing to an MQTT topic, "+" and "#" can be used as wildcards ([more details](https://mosquitto.org/man/mqtt-7.html)).

This works with Quarkus 1.4.2-Final but stops working when switching to Quarkus 1.5.0-Final.

## Preparation

### Clone project for demonstrating the error:
```
git clone https://github.com/buehren/quarkus-1.5.0-mqtt-wildcard-problem.git
cd quarkus-1.5.0-mqtt-wildcard-problem
```

### Start MQTT server (Mosquitto)
```
docker-compose up -d
```


## See it working with Quarkus 1.4.2-Final
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
* Every 5 seconds a number appears in CURL's output.
* In the Quarkus output, each "Sending price" line is now followed by "Receiving price".

Press CTRL+C to stop Quarkus.


## See error with Quarkus 1.5.0-Final
```
git checkout quarkus-1.5.0

git diff master quarkus-1.5.0
# Output shows that only the Quarkus version is changed

egrep "^mp.messaging.incoming.prices.topic=" src/main/resources/application.properties
# Output:
# mp.messaging.incoming.prices.topic=#

egrep "<quarkus.*version" pom.xml
# Output:
#     <quarkus-plugin.version>1.5.0.Final</quarkus-plugin.version>
#     <quarkus.platform.version>1.5.0.Final</quarkus.platform.version>

mvn clean quarkus:dev
```
Every 5 seconds a line "Sending price" appears.

In another shell:
```
curl localhost:8080/prices/stream
```

Result (ERROR):
* No output appears in CURL's output.
* In the Quarkus output, each "Sending price" line is NOT followed by "Receiving price".

Press CTRL+C to stop Quarkus.


## See it working with Quarkus 1.5.0-Final - but without MQTT wildcard
```
git checkout quarkus-1.5.0-no-wildcard

git diff quarkus-1.5.0 quarkus-1.5.0-no-wildcard
# Output shows that only the topic wildcard in the subscription is no longer used

egrep "^mp.messaging.incoming.prices.topic=" src/main/resources/application.properties
# Output:
# mp.messaging.incoming.prices.topic=prices

egrep "<quarkus.*version" pom.xml
# Output:
#     <quarkus-plugin.version>1.5.0.Final</quarkus-plugin.version>
#     <quarkus.platform.version>1.5.0.Final</quarkus.platform.version>

mvn clean quarkus:dev
```
Every 5 seconds a line "Sending price" appears.

In another shell:
```
curl localhost:8080/prices/stream
```

Result (as expected):
* Every 5 seconds a number appears in CURL's output.
* In the Quarkus output, each "Sending price" line is now followed by "Receiving price".

**BUT: MQTT subscription topic wildcards should not stop working with Quarkus 1.5!**

Press CTRL+C to stop Quarkus.

## Cleanup

### Stop MQTT server (Mosquitto)
```
docker-compose down
```
