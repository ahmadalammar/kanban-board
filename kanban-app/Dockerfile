FROM maven:3.6.1-jdk-8-slim AS build
RUN mkdir -p /workspace
WORKDIR /workspace
COPY pom.xml /workspace
COPY src /workspace/src
RUN mvn -f pom.xml clean package

FROM openjdk:8-alpine

# Download and configure OpenTelemetry agent
RUN wget -O /opentelemetry-javaagent.jar https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar

COPY --from=build /workspace/target/*.jar /app.jar
EXPOSE 8080

ENTRYPOINT java -javaagent:/opentelemetry-javaagent.jar \
                -Dotel.traces.exporter=otlp \
                -Dotel.metrics.exporter=otlp \
                -Dotel.logs.exporter=otlp \
                -Dotel.exporter.otlp.endpoint=http://otel-collector:4318 \
                -Dotel.service.name=kanban-ui \
                -jar app.jar
