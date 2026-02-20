---
name: Java Project Setup
description: Guidelines document for Java project setup
version: 1.0.0
modified: 2026-02-20
---
# Java Project Setup Guide

**Target:** Java 21 LTS, Maven 3.9+ or Gradle 8+  
**Reading time:** 35 minutes

---

## Table of contents

1. [Quick start](#quick-start)
2. [Universal baseline setup](#universal-baseline-setup)
3. [Standalone applications](#standalone-applications)
4. [Library projects](#library-projects)
5. [Web applications](#web-applications)
6. [Microservices](#microservices)
7. [CLI applications](#cli-applications)
8. [Configuration reference](#configuration-reference)
9. [Verification checklist](#verification-checklist)

---

## Quick start

Choose your project type and navigate to the relevant section:

| Project type | Use case | Primary framework | Section |
|:-------------|:---------|:------------------|:--------|
| **Standalone application** | Batch jobs, data migration, desktop tools | None (vanilla Java) | [Standalone](#standalone-applications) |
| **Library/SDK** | Reusable components, shared utilities | None | [Library](#library-projects) |
| **Web application** | REST APIs, server-rendered UIs, monoliths | Spring Boot 3.x | [Web app](#web-applications) |
| **Microservice** | Cloud-native services, independent deployment | Spring Boot 3.x or Quarkus 3.x | [Microservices](#microservices) |
| **CLI tool** | DevOps scripts, admin tools, data processors | Picocli or Spring Shell | [CLI](#cli-applications) |

> **IMPORTANT:** Regardless of project type, domain code **MUST NOT** depend on HTTP frameworks, database drivers, or external SDKs. Maintain strict separation of concerns between business logic and infrastructure.

---

## Universal baseline setup

These steps apply to every Java project before type-specific configuration.

### Prerequisites

Verify required tools are installed:

```bash
# Check Java version (21+ required)
java -version

# Check Maven (3.9+) or Gradle (8+)
mvn -version
# or
gradle -version

# Check Git
git --version
```

Install missing tools:

- **Java 21**: Download from [Adoptium](https://adoptium.net/) or use [SDKMAN](https://sdkman.io/)
- **Maven**: [Download](https://maven.apache.org/download.cgi) or use package manager
- **Gradle**: [Download](https://gradle.org/install/) or use package manager

### Step 1: Initialize repository structure

Create the standardized directory layout:

```bash
mkdir -p src/main/java/com/example/project
mkdir -p src/test/java/com/example/project
mkdir -p src/main/resources
mkdir -p src/test/resources
mkdir -p docs/architecture/adr
mkdir -p docs/development
mkdir -p .github/workflows

# Initialize Git
git init
```

### Step 2: Configure version control

Create `.gitignore`:

```gitignore
# Build outputs
/target/
/build/
/out/
*.class
*.jar
*.war

# IDE files
/.idea/
/*.iml
/.vscode/
/.classpath
/.project
/.settings/

# OS files
.DS_Store
Thumbs.db

# Logs and environment
*.log
.env
.env.local
.env.*.local

# Maven
/.mvn/wrapper/maven-wrapper.jar

# Gradle
/.gradle/
/gradle/
/gradlew
/gradlew.bat
```

Create `.gitattributes`:

```gitattributes
* text=auto eol=lf
*.bat text eol=crlf
```

Create `.editorconfig`:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.java]
indent_style = space
indent_size = 2

[*.{xml,yml,yaml}]
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
max_line_length = 120
```

### Step 3: Create baseline build configuration

Choose Maven or Gradle, then apply type-specific additions in later sections.

#### Option A: Maven baseline

Create `pom.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0 "
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance "
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         https://maven.apache.org/xsd/maven-4.0.0.xsd ">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>project-name</artifactId>
  <version>0.1.0-SNAPSHOT</version>
  <name>project-name</name>
  <description>Project description</description>

  <properties>
    <maven.compiler.release>21</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    
    <!-- Tool versions -->
    <junit.jupiter.version>5.10.2</junit.jupiter.version>
    <mockito.version>5.11.0</mockito.version>
    <spotless.version>2.43.0</spotless.version>
    <googlejavaformat.version>1.22.0</googlejavaformat.version>
    <spotbugs.version>4.8.6.6</spotbugs.version>
    <dependencycheck.version>10.0.4</dependencycheck.version>
    <jacoco.version>0.8.12</jacoco.version>
  </properties>

  <dependencies>
    <!-- Logging facade -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>2.0.13</version>
    </dependency>

    <!-- Testing -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-junit-jupiter</artifactId>
      <version>${mockito.version}</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.assertj</groupId>
      <artifactId>assertj-core</artifactId>
      <version>3.25.3</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <!-- Enforce Java and Maven versions -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-enforcer-plugin</artifactId>
        <version>3.5.0</version>
        <executions>
          <execution>
            <id>enforce</id>
            <goals><goal>enforce</goal></goals>
            <configuration>
              <rules>
                <requireJavaVersion>
                  <version>[21,)</version>
                </requireJavaVersion>
                <requireMavenVersion>
                  <version>[3.9,)</version>
                </requireMavenVersion>
              </rules>
            </configuration>
          </execution>
        </executions>
      </plugin>

      <!-- Formatting: Spotless + Google Java Format -->
      <plugin>
        <groupId>com.diffplug.spotless</groupId>
        <artifactId>spotless-maven-plugin</artifactId>
        <version>${spotless.version}</version>
        <configuration>
          <java>
            <googleJavaFormat>
              <version>${googlejavaformat.version}</version>
              <style>GOOGLE</style>
            </googleJavaFormat>
            <removeUnusedImports/>
            <importOrder/>
          </java>
        </configuration>
        <executions>
          <execution>
            <id>spotless-check</id>
            <goals><goal>check</goal></goals>
            <phase>verify</phase>
          </execution>
        </executions>
      </plugin>

      <!-- Bug patterns: SpotBugs -->
      <plugin>
        <groupId>com.github.spotbugs</groupId>
        <artifactId>spotbugs-maven-plugin</artifactId>
        <version>${spotbugs.version}</version>
        <configuration>
          <effort>Max</effort>
          <threshold>Low</threshold>
        </configuration>
        <executions>
          <execution>
            <id>spotbugs</id>
            <goals><goal>check</goal></goals>
            <phase>verify</phase>
          </execution>
        </executions>
      </plugin>

      <!-- Security scanning: OWASP Dependency-Check -->
      <plugin>
        <groupId>org.owasp</groupId>
        <artifactId>dependency-check-maven</artifactId>
        <version>${dependencycheck.version}</version>
        <configuration>
          <failBuildOnCVSS>7</failBuildOnCVSS>
          <assemblyAnalyzerEnabled>false</assemblyAnalyzerEnabled>
        </configuration>
        <executions>
          <execution>
            <id>dependency-check</id>
            <goals><goal>check</goal></goals>
            <phase>verify</phase>
          </execution>
        </executions>
      </plugin>

      <!-- Unit tests -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.3.1</version>
      </plugin>

      <!-- Integration tests: *IT.java -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-failsafe-plugin</artifactId>
        <version>3.3.1</version>
        <executions>
          <execution>
            <id>integration-tests</id>
            <goals>
              <goal>integration-test</goal>
              <goal>verify</goal>
            </goals>
            <configuration>
              <includes>
                <include>**/*IT.java</include>
              </includes>
            </configuration>
          </execution>
        </executions>
      </plugin>

      <!-- Coverage: JaCoCo -->
      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>${jacoco.version}</version>
        <executions>
          <execution>
            <id>jacoco-prepare-agent</id>
            <goals><goal>prepare-agent</goal></goals>
          </execution>
          <execution>
            <id>jacoco-report</id>
            <phase>verify</phase>
            <goals><goal>report</goal></goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```

#### Option B: Gradle baseline

Create `build.gradle`:

```gradle
plugins {
  id 'java'
  id 'jacoco'
  id 'com.diffplug.spotless' version '6.25.0'
  id 'com.github.spotbugs' version '6.0.26'
  id 'org.owasp.dependencycheck' version '9.2.0'
}

group = 'com.example'
version = '0.1.0-SNAPSHOT'

java {
  toolchain {
    languageVersion = JavaLanguageVersion.of(21)
  }
}

repositories {
  mavenCentral()
}

dependencies {
  implementation 'org.slf4j:slf4j-api:2.0.13'
  
  testImplementation platform('org.junit:junit-bom:5.10.2')
  testImplementation 'org.junit.jupiter:junit-jupiter'
  testImplementation 'org.mockito:mockito-junit-jupiter:5.11.0'
  testImplementation 'org.assertj:assertj-core:3.25.3'
}

test {
  useJUnitPlatform()
}

spotless {
  java {
    googleJavaFormat('1.22.0')
    removeUnusedImports()
    importOrder()
  }
}

spotbugs {
  effort = com.github.spotbugs.snom.Effort.MAX
  reportLevel = com.github.spotbugs.snom.Confidence.LOW
}

dependencyCheck {
  failBuildOnCVSS = 7.0
}

jacocoTestReport {
  dependsOn test
}
```

### Step 4: Configure logging

Create `src/main/resources/logback.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{yyyy-MM-dd'T'HH:mm:ss.SSS} %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="${APP_LOG_LEVEL:-INFO}">
    <appender-ref ref="STDOUT"/>
  </root>
</configuration>
```

> **WARNING:** Never commit sensitive data in log configuration. Use environment variables for environment-specific settings.

### Step 5: Create documentation structure

Create `README.md`:

```markdown
# Project Name

Brief description of what this project does and why it exists.

## Prerequisites

- Java 21 or later
- Maven 3.9+ or Gradle 8+

## Quick start

```bash
# Build the project
mvn clean install

# Run tests
mvn test

# Format code
mvn spotless:apply
```

## Configuration

Copy `.env.example` to `.env` for local development. Do not commit `.env`.

## Documentation

- [Setup guide](docs/development/setup.md)
- [Architecture](docs/architecture/architecture.md)
- [API reference](docs/api/llm-context.md)

## License

[Specify license]
```

Create `CHANGELOG.md`:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial project setup

## [0.1.0] - YYYY-MM-DD

### Added
- Project structure
- Build configuration
```

Create `.env.example`:

```bash
# Copy to .env for local use. Do not commit .env.
APP_ENV=local
APP_LOG_LEVEL=INFO
```

---

## Standalone applications

Standalone applications are single-purpose programs with a `main` method that run independently and exit.

### When to use

- Batch processing jobs
- Data migration scripts
- One-time automation tasks
- Desktop applications (non-GUI)

### Setup steps

#### 1. Add executable JAR configuration (Maven)

Add to `pom.xml` inside `<build><plugins>`:

```xml
<!-- Executable JAR with dependencies -->
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-shade-plugin</artifactId>
  <version>3.5.2</version>
  <executions>
    <execution>
      <phase>package</phase>
      <goals><goal>shade</goal></goals>
      <configuration>
        <transformers>
          <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
            <mainClass>com.example.Application</mainClass>
          </transformer>
        </transformers>
      </configuration>
    </execution>
  </executions>
</plugin>
```

#### 2. Create application entry point

Create `src/main/java/com/example/Application.java`:

```java
package com.example;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * Main application entry point.
 *
 * <p>This application demonstrates a standalone Java application structure following modern Java
 * practices and engineering standards.
 *
 * @since 0.1.0
 */
public final class Application {
  private static final Logger log = LoggerFactory.getLogger(Application.class);

  private Application() {}

  /**
   * Application entry point.
   *
   * @param args command-line arguments
   */
  public static void main(String[] args) {
    log.info("Application starting");

    try {
      var app = new Application();
      int exitCode = app.run(args);
      log.info("Application completed successfully");
      System.exit(exitCode);
    } catch (Exception e) {
      log.error("Application failed", e);
      System.exit(1);
    }
  }

  /**
   * Executes the main application logic.
   *
   * @param args command-line arguments
   * @return exit code (0 for success)
   */
  public int run(String[] args) {
    log.info("Running with {} arguments", args.length);
    // Application logic here
    return 0;
  }
}
```

#### 3. Build and run

```bash
# Format code
mvn spotless:apply

# Build executable JAR
mvn clean package

# Run the application
java -jar target/project-name-0.1.0-SNAPSHOT.jar
```

---

## Library projects

Libraries provide reusable components for other Java applications. They have no `main` method and are distributed as JAR artifacts.

### When to use

- Shared utilities across multiple projects
- SDKs for external API integration
- Framework components
- Data models shared across services

### Setup steps

#### 1. Configure library-specific POM

Update `pom.xml`:

```xml
<packaging>jar</packaging>

<dependencies>
  <!-- Remove runtime logging implementation -->
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.5.6</version>
    <scope>test</scope>
  </dependency>
</dependencies>

<build>
  <plugins>
    <!-- Javadoc generation (required for libraries) -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-javadoc-plugin</artifactId>
      <version>3.6.3</version>
      <configuration>
        <source>21</source>
        <show>public</show>
        <failOnError>true</failOnError>
      </configuration>
      <executions>
        <execution>
          <id>attach-javadocs</id>
          <goals><goal>jar</goal></goals>
        </execution>
      </executions>
    </plugin>

    <!-- Source JAR (required for libraries) -->
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-source-plugin</artifactId>
      <version>3.3.0</version>
      <executions>
        <execution>
          <id>attach-sources</id>
          <goals><goal>jar</goal></goals>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

#### 2. Create public API

Create `src/main/java/com/example/library/IdGenerator.java`:

```java
package com.example.library;

import java.util.UUID;

/**
 * Generates identifiers for external-facing resources.
 *
 * <p>Thread safety: Implementations MUST be thread-safe.
 *
 * @since 0.1.0
 */
public interface IdGenerator {

  /**
   * Generate a new identifier.
   *
   * @return a new identifier (never {@code null})
   * @since 0.1.0
   */
  UUID newId();
}
```

Create implementation `src/main/java/com/example/library/UuidGenerator.java`:

```java
package com.example.library;

import java.util.UUID;

/**
 * UUID-based identifier generator.
 *
 * @since 0.1.0
 */
public final class UuidGenerator implements IdGenerator {

  @Override
  public UUID newId() {
    return UUID.randomUUID();
  }
}
```

#### 3. Package structure

```
src/main/java/com/example/library/
├── api/                    # Public interfaces
│   └── IdGenerator.java
├── internal/               # Package-private implementations
│   └── UuidGenerator.java
└── exception/              # Custom exceptions
    └── LibraryException.java
```

> **NOTE:** Keep the public API surface minimal and stable. Use `internal` packages for implementation details.

---

## Web applications

Web applications expose HTTP APIs, typically RESTful services using Spring Boot.

### When to use

- REST APIs
- Backend for frontend (BFF)
- Traditional web services
- Monolithic applications

### Setup steps

#### 1. Add Spring Boot parent and dependencies

Update `pom.xml`:

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.2.4</version>
  <relativePath/>
</parent>

<dependencies>
  <!-- Web -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  
  <!-- Validation -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
  </dependency>
  
  <!-- Actuator (health checks) -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>

  <!-- Testing -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
  <dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>

<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

#### 2. Configure application

Create `src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: my-web-app

server:
  port: 8080
  shutdown: graceful

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
  endpoint:
    health:
      probes:
        enabled: true

logging:
  level:
    root: INFO
    com.example: DEBUG
```

#### 3. Create layered architecture

**Domain layer** (independent of Spring):

```java
// src/main/java/com/example/domain/Greeter.java
package com.example.domain;

/**
 * Domain service that creates greeting messages.
 *
 * @since 0.1.0
 */
public interface Greeter {
  String greet(String name);
}
```

```java
// src/main/java/com/example/domain/DefaultGreeter.java
package com.example.domain;

/**
 * Default greeter implementation.
 *
 * @since 0.1.0
 */
public final class DefaultGreeter implements Greeter {

  @Override
  public String greet(String name) {
    return "Hello, " + name + "!";
  }
}
```

**Application layer** (Spring-aware):

```java
// src/main/java/com/example/application/GreetingController.java
package com.example.application;

import com.example.domain.Greeter;
import jakarta.validation.Valid;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

/**
 * HTTP API for greetings.
 *
 * @since 0.1.0
 */
@RestController
public final class GreetingController {
  private final Greeter greeter;

  public GreetingController(Greeter greeter) {
    this.greeter = greeter;
  }

  @PostMapping("/api/greet")
  public GreetingResponse greet(@Valid @RequestBody GreetingRequest request) {
    return new GreetingResponse(greeter.greet(request.name()));
  }

  public record GreetingRequest(@jakarta.validation.constraints.NotBlank String name) {}

  public record GreetingResponse(String message) {}
}
```

**Configuration**:

```java
// src/main/java/com/example/config/DomainConfig.java
package com.example.config;

import com.example.domain.DefaultGreeter;
import com.example.domain.Greeter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class DomainConfig {

  @Bean
  public Greeter greeter() {
    return new DefaultGreeter();
  }
}
```

#### 4. Application entry point

```java
// src/main/java/com/example/Application.java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Spring Boot entry point.
 *
 * @since 0.1.0
 */
@SpringBootApplication
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```

---

## Microservices

Microservices extend web applications with cloud-native concerns: service discovery, distributed tracing, resilience patterns, and containerization.

### When to use

- Cloud-native applications
- Services requiring independent scaling
- Domain-driven design boundaries
- Event-driven architectures

### Setup steps

#### 1. Add cloud-native dependencies (Spring Boot)

Update `pom.xml`:

```xml
<dependencies>
  <!-- Existing web dependencies -->
  
  <!-- Resilience4j for circuit breakers -->
  <dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
    <version>2.2.0</version>
  </dependency>

  <!-- Distributed tracing -->
  <dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
  </dependency>
  <dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter-brave</artifactId>
  </dependency>

  <!-- Prometheus metrics -->
  <dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
  </dependency>
</dependencies>

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>2023.0.0</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

#### 2. Configure production-ready settings

Update `src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: my-microservice

server:
  port: ${PORT:8080}
  shutdown: graceful

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      probes:
        enabled: true
      show-details: when-authorized
  metrics:
    tags:
      application: ${spring.application.name}

resilience4j:
  circuitbreaker:
    instances:
      default:
        sliding-window-size: 10
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10s
```

#### 3. Add containerization

Create `Dockerfile`:

```dockerfile
# Build stage
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Runtime stage
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Create `k8s/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-microservice
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-microservice
  template:
    metadata:
      labels:
        app: my-microservice
    spec:
      containers:
      - name: app
        image: my-microservice:latest
        ports:
        - containerPort: 8080
        env:
        - name: JAVA_OPTS
          value: "-Xmx512m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 20
          periodSeconds: 5
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: my-microservice
spec:
  selector:
    app: my-microservice
  ports:
  - port: 80
    targetPort: 8080
```

#### 4. Outbound HTTP client with timeouts

Create `src/main/java/com/example/infrastructure/client/HttpClientConfig.java`:

```java
package com.example.infrastructure.client;

import java.net.http.HttpClient;
import java.time.Duration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class HttpClientConfig {

  @Bean
  public HttpClient httpClient() {
    return HttpClient.newBuilder()
        .connectTimeout(Duration.ofSeconds(5))
        .build();
  }
}
```

> **IMPORTANT:** Always configure timeouts for outbound calls. Never use default timeouts in production.

---

## CLI applications

CLI applications provide command-line interfaces for interactive or scripted use.

### When to use

- DevOps and admin tools
- Data processing scripts
- Build automation tools
- Interactive shells

### Setup steps

#### Option A: Picocli (recommended for standalone CLI)

Add dependency to `pom.xml`:

```xml
<dependency>
  <groupId>info.picocli</groupId>
  <artifactId>picocli</artifactId>
  <version>4.7.5</version>
</dependency>
```

Create `src/main/java/com/example/cli/CliApplication.java`:

```java
package com.example.cli;

import java.io.File;
import java.util.concurrent.Callable;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import picocli.CommandLine;
import picocli.CommandLine.Command;
import picocli.CommandLine.Option;
import picocli.CommandLine.Parameters;

/**
 * CLI entry point using Picocli.
 *
 * @since 0.1.0
 */
@Command(
    name = "mycli",
    mixinStandardHelpOptions = true,
    version = "mycli 0.1.0",
    description = "A sample CLI tool")
public class CliApplication implements Callable<Integer> {
  private static final Logger log = LoggerFactory.getLogger(CliApplication.class);

  @Parameters(index = "0", description = "The file to process")
  private File inputFile;

  @Option(
      names = {"-o", "--output"},
      description = "Output file")
  private File outputFile;

  @Option(
      names = {"-v", "--verbose"},
      description = "Enable verbose output")
  private boolean verbose;

  public static void main(String[] args) {
    int exitCode = new CommandLine(new CliApplication()).execute(args);
    System.exit(exitCode);
  }

  @Override
  public Integer call() {
    try {
      if (verbose) {
        log.info("Processing file: {}", inputFile);
      }

      if (!inputFile.exists()) {
        System.err.println("Error: Input file does not exist: " + inputFile);
        return 1;
      }

      processFile(inputFile, outputFile);
      return 0;
    } catch (Exception e) {
      log.error("Processing failed", e);
      return 1;
    }
  }

  private void processFile(File input, File output) {
    log.info("Processing {} -> {}", input, output != null ? output : "stdout");
  }
}
```

#### Option B: Spring Shell (for interactive applications)

Generate project from [start.spring.io](https://start.spring.io) with `shell` dependency, or add:

```xml
<dependency>
  <groupId>org.springframework.shell</groupId>
  <artifactId>spring-shell-starter</artifactId>
  <version>3.2.0</version>
</dependency>
```

Create commands:

```java
package com.example.shell;

import org.springframework.shell.standard.ShellComponent;
import org.springframework.shell.standard.ShellMethod;
import org.springframework.shell.standard.ShellOption;

@ShellComponent
public class MyCommands {

  @ShellMethod(value = "Greet someone", key = "greet")
  public String greet(@ShellOption(defaultValue = "World") String name) {
    return "Hello, " + name + "!";
  }
}
```

---

## Configuration reference

### Quality gates configuration

All projects **MUST** enable these quality gates in the build:

| Tool | Purpose | Maven command | Phase |
|:-----|:--------|:--------------|:------|
| Spotless | Formatting enforcement | `mvn spotless:check` | verify |
| SpotBugs | Bug pattern detection | `mvn spotbugs:check` | verify |
| OWASP Dependency-Check | Vulnerability scanning | `mvn dependency-check:check` | verify |
| JaCoCo | Test coverage | `mvn jacoco:report` | verify |
| Surefire | Unit tests | `mvn test` | test |
| Failsafe | Integration tests | `mvn verify` | verify |

### Standard Maven commands

```bash
# Format code (auto-fix)
mvn spotless:apply

# Run all checks (formatting, tests, security, coverage)
mvn clean verify

# Run only unit tests
mvn test

# Run integration tests
mvn failsafe:integration-test

# Check for vulnerabilities
mvn dependency-check:check

# Generate coverage report
mvn jacoco:report
# View at target/site/jacoco/index.html
```

### Standard Gradle commands

```bash
# Format code
./gradlew spotlessApply

# Run all checks
./gradlew check

# Run tests
./gradlew test

# Generate coverage report
./gradlew jacocoTestReport
```

---

## Verification checklist

Before committing code, verify:

- [ ] **Build passes**: `mvn clean verify` or `./gradlew check` succeeds
- [ ] **Formatting**: Spotless check passes (enforced by build)
- [ ] **Static analysis**: SpotBugs reports no errors
- [ ] **Security**: OWASP Dependency-Check passes (CVSS < 7)
- [ ] **Tests**: All unit and integration tests pass
- [ ] **Coverage**: JaCoCo report generated, meets team thresholds
- [ ] **No secrets**: `.env` is ignored, no credentials in code
- [ ] **Documentation**: README.md, CHANGELOG.md, and `/docs` structure exist
- [ ] **Architecture boundaries**: Domain layer has no Spring/web dependencies

---

**Next steps:** Choose your project type from the table of contents and follow the specific setup instructions.
