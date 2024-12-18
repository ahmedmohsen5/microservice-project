# Build stage
FROM gradle:8.6-jdk17 AS build

WORKDIR /app

# Copy gradle files first
COPY gradlew .
COPY gradlew.bat .
COPY settings.gradle .
COPY build.gradle .
COPY gradle ./gradle

# Make gradlew executable
RUN chmod +x ./gradlew

# Download dependencies
RUN ./gradlew dependencies --no-daemon

# Copy source code and proto files
COPY src ./src

# Build the application
RUN ./gradlew clean build -x test --no-daemon

# Runtime stage
FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

# Copy the built artifact from build stage
COPY --from=build /app/build/libs/*.jar app.jar

# Default gRPC port
EXPOSE 9555

# Create a non-root user to run the application
RUN useradd -m myuser
USER myuser

# Set the startup command with gRPC port
ENTRYPOINT ["java", \
    "-Dgrpc.server.port=9555", \
    # Add any additional JVM options here
    "-jar", \
    "app.jar" \
]

# Health check for gRPC (if you have a health check endpoint)
# Note: You might need to install grpc_health_probe for proper gRPC health checks
# HEALTHCHECK --interval=30s --timeout=3s \
#   CMD ["/bin/grpc_health_probe", "-addr=:9555"] || exit 1