# Use official OpenJDK 17 image (lightweight)
FROM openjdk:17-jdk-slim AS build

# Set working directory
WORKDIR /app

# Copy Maven wrapper (if available) and pom.xml first for dependency caching
COPY pom.xml ./
COPY mvnw ./
COPY .mvn .mvn

# Download dependencies (cache layer)
RUN ./mvnw dependency:go-offline -B || apt-get update && apt-get install -y maven && mvn dependency:go-offline -B

# Copy the entire project
COPY . .

# Build the Spring Boot application
RUN ./mvnw clean package -DskipTests || mvn clean package -DskipTests

# ===============================
# Final lightweight runtime image
# ===============================
FROM openjdk:17-jdk-slim

# Set working directory
WORKDIR /app

# Copy only the JAR from build stage
COPY --from=build /app/target/*.jar app.jar

# Expose port 8080
EXPOSE 8080

# Start the Spring Boot application
ENTRYPOINT ["java", "-jar", "app.jar"]
