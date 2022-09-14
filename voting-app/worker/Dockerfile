FROM maven:3.5-jdk-8-alpine AS build

WORKDIR /code

RUN mkdir melissa
COPY src/settings.xml melissa
COPY src/pom.xml /code/pom.xml

RUN ["mvn", "-s /melissa/settings.xml", "dependency:resolve"]
RUN ["mvn", "-s /melissa/settings.xml", "verify"]

# Adding source, compile and package into a fat jar
COPY ["src/src/main", "/code/src/main"]
RUN ["mvn", "-s /melissa/settings.xml", "package"]

FROM openjdk:8-jre

WORKDIR /app

RUN useradd -u 1001 -r -g 0 -d /app -s /sbin/nologin \
    -c "Default Application User" default && \
    chown -R 1001:0 /app && \
    chmod -R g+rw /app

USER 1001

COPY --from=build /code/target/worker-jar-with-dependencies.jar /

CMD ["java", "-XX:+UnlockExperimentalVMOptions", "-XX:+UseCGroupMemoryLimitForHeap", "-jar", "/worker-jar-with-dependencies.jar"]
