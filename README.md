# Quarkus Webinar Demo by Erfin

## generate quarkus project
```bash
cd ~/Desktop/webinar-quarkus

mvn io.quarkus:quarkus-maven-plugin:1.6.0.Final:create \
    -DprojectGroupId=com.erfinfeluzy \
    -DprojectArtifactId=webinar-quarkus \
    -DclassName="com.erfinfeluzy.GreetingResource" \
    -Dpath="/hello" \
    -Dextensions="health,resteasy-jackson"
```

## run as Development mode
```
cd ~/Desktop/webinar-quarkus/webinar-quarkus

mvn quarkus:dev

curl http://localhost:8080/hello
```

## Hot Deploy Demo
open java file com.erfinfeluzy.GreetingResource, add below line. PS: dont foget to import @PathParam
```
@Path("/{name}")
    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String helloName(@PathParam("name") String name) {
        return "hello " + name;
    }
```
try hot deploy
```
curl http://localhost:8080/hello/sayang
```

## build as container native
monitor docker on separate window
```
docker stats
```

### build container native image
```
mvn clean package -Pnative -Dquarkus.native.container-build=true -DskipTests=true
```

#### Notes: if build took so long
```
docker stop $INSTANCE_NAME
```

## view generated quarkus executable
```
ls -ltr target/*-runner
```

## build docker images
```
docker build -f src/main/docker/Dockerfile.native -t webinar-quarkus:native .

docker images | grep webinar
```

## run docker images native vs hotspot mode
```
docker run --name quarkus-jvm -i --rm -p 8081:8080 webinar-quarkus:jvm
docker run --name quarkus-native -i --rm -p 8080:8080 webinar-quarkus:native
```
#### view docker statistic
native quarkus
```
for i in {1..1000}; do curl http://localhost:8080/hello/sayang; done
```
quarkus on jvm
```
for i in {1..1000}; do curl http://localhost:8081/hello/sayang; done
```

# serverless
On openshift develoer console, deploy new container
```
quay.io/efeluzy/quarkus-kafka-consumer:v3 as serverless
quay.io/efeluzy/quarkus-kafka-producer:v3
```
