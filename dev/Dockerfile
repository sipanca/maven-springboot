## Build Project Springboot
FROM maven:3.9-eclipse-temurin-17-alpine AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests
 
## Build Docker Image
FROM eclipse-temurin:17.0.5_8-jre-alpine@sha256:02c04793fa49ad5cd193c961403223755f9209a67894622e05438598b32f210e
WORKDIR /app
EXPOSE 8080
COPY --from=builder /app/target/springboot-demo-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]