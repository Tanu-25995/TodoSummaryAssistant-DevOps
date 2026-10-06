FROM maven:3.9.9-eclipse-temurin-17 AS build

WORKDIR /build

COPY pom.xml .
COPY mvnw .
COPY .mvn .mvn

RUN chmod +x mvnw
RUN ./mvnw dependency:go-offline

COPY src src

RUN ./mvnw clean package -DskipTests


FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=build /build/target/todo-summary-assistant-0.0.1-SNAPSHOT.jar app.jar

RUN useradd -r -u 1001 appuser

USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
