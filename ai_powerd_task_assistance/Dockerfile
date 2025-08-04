# --- Stage 1: Build the application ---
# Use a Maven image to build the application JAR file.
FROM maven:3.8.5-openjdk-17 AS build

# Set the working directory inside the container
WORKDIR /app

# Copy the POM file to download dependencies
COPY pom.xml .

# Download all dependencies into the Maven repository.
# This creates a separate layer that is cached by Docker.
RUN mvn dependency:go-offline

# Copy the rest of the source code
COPY src ./src

# Package the application, skipping tests for faster builds
RUN mvn package -DskipTests

# --- Stage 2: Create the final, lean image ---
# Use a slim OpenJDK image which is much smaller than the Maven image.
FROM openjdk:17-slim

# Set the working directory
WORKDIR /app

# Expose the port the application runs on
EXPOSE 8080

# Copy the JAR file from the 'build' stage
COPY --from=build /app/target/*.jar app.jar

# Run the JAR file when the container launches
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
