# ---------- Build Stage ----------
ARG JAVA_VERSION=11
FROM maven:3.8.6-openjdk-${JAVA_VERSION} AS builder

LABEL stage=builder

WORKDIR /app

# Copy Maven configuration
COPY pom.xml ./

# Download dependencies (cache layer)
RUN mvn -B dependency:resolve dependency:resolve-plugins

# Copy source code
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# ---------- Runtime Stage ----------
FROM eclipse-temurin:${JAVA_VERSION}-jre-jammy

LABEL maintainer="Fahad Jaseem"
LABEL version="1.0"
LABEL description="Employee Management Backend"

WORKDIR /app

# JVM options
ENV JAVA_OPTS="-XX:TieredStopAtLevel=1 -XX:+UseSerialGC -Dspring.main.lazy-initialization=true -Dspring.jmx.enabled=false"

# Copy application
COPY --from=builder /app/target/employee-management-app-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=30s --retries=3 \
CMD wget --spider http://localhost:8080/api/health || exit 1

ENTRYPOINT ["sh","-c","java $JAVA_OPTS -jar app.jar"]


#alternate way to write healthcheck
# HEALTHCHECK \
#     --interval=30s \
#     --timeout=5s \
#     --start-period=60s \
#     --retries=3 \
# CMD curl -f http://localhost:8080/api/health || exit 1