# FROM maven:3.9.0-eclipse-temurin-17 AS build
# COPY . .
# RUN mvn clean package -DskipTests

# FROM eclipse-temurin:17-alpine
# COPY --from=build /target/aibackend-*.jar aibackend.jar
# EXPOSE 8080
# CMD ["java", "-jar", "aibackend.jar"]

# ┌──────────────────────────────┐
# │  Stage 1: Build with Maven  │
# └──────────────────────────────┘
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app

# 1) Copy only pom.xml first (caches dependencies)
COPY pom.xml . 
RUN mvn dependency:go-offline -B

# 2) Copy source code
COPY src ./src

# 3) Build the JAR with Java 21
RUN mvn clean package -DskipTests


# ┌───────────────────────────────────────┐
# │  Stage 2: Runtime (Java 21 JRE)       │
# └───────────────────────────────────────┘
FROM eclipse-temurin:21-alpine AS runtime
WORKDIR /app

# 4) Copy the built JAR from stage “build”
COPY --from=build /app/target/aibackend-0.0.1-SNAPSHOT.jar app.jar

# 5) Expose Spring Boot’s default port
EXPOSE 8080

# 6) Entrypoint: run with container-aware JVM flags
ENTRYPOINT ["java", \
            "-XX:+UseContainerSupport", \
            "-XX:MaxRAMPercentage=75.0", \
            "-jar", \
            "app.jar"]
