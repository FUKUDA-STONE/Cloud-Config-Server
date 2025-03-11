# Spring Cloud Config Server

## Overview
Spring Cloud Config Server is a centralized configuration server that provides externalized configuration for distributed applications. It allows microservices to retrieve configuration properties from a remote repository (e.g., Git) based on different environments (e.g., `dev`, `qa`, `prod`).

## Configuration Structure
The configuration files are stored in the `resources/config` directory and are structured as follows:

```
config/
│── accounts.yml
│── accounts-prod.yml
│── accounts-qa.yml
│── cards.yml
│── cards-prod.yml
│── cards-qa.yml
│── loans.yml
│── loans-prod.yml
│── loans-qa.yml
│── application.yml
```

- **`accounts.yml`**, `cards.yml`, `loans.yml` → Default configurations for each service.
- **`*-prod.yml`** → Configuration for the production environment.
- **`*-qa.yml`** → Configuration for the QA (testing) environment.
- **`application.yml`** → General settings for the Config Server.

## Running the Config Server
To run the Config Server, ensure that your `application.yml` contains:

```yaml
server:
  port: 8888

spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/your-org/your-config-repo.git
          clone-on-start: true
```

Start the server using:
```sh
mvn spring-boot:run
```

## Fetching Configuration from Config Server
To retrieve configurations from the Config Server, clients (microservices) should have a `bootstrap.yml` or `application.yml` with the following content:

```yaml
spring:
  application:
    name: accounts
  config:
    import: optional:configserver:http://localhost:8888
```

### Fetch Default Configuration
```
GET http://localhost:8888/accounts/default
```
### Fetch Production Configuration
```
GET http://localhost:8888/accounts/prod
```
### Fetch QA Configuration
```
GET http://localhost:8888/accounts/qa
```

## Setting Up Profiles
To use a specific profile (`prod`, `qa`), you can:

### **1. Define Profile in `application.yml`**
```yaml
spring:
  profiles:
    active: prod
```

### **2. Pass Profile via Command Line**
```sh
java -jar accounts-service.jar --spring.profiles.active=prod
```

### **3. Use Environment Variables**
**Linux/macOS:**
```sh
export SPRING_PROFILES_ACTIVE=prod
```
**Windows CMD:**
```sh
set SPRING_PROFILES_ACTIVE=prod
```
**Windows PowerShell:**
```powershell
$env:SPRING_PROFILES_ACTIVE="prod"
```

## Summary
- **Spring Cloud Config Server** centralizes configuration management.
- **Profiles (`prod`, `qa`, `default`)** allow different settings for different environments.
- **Microservices fetch their configuration** from Config Server dynamically.
- **Use `bootstrap.yml` or `application.yml`** to connect to Config Server.

This setup ensures that configuration management is scalable, maintainable, and flexible for microservices architecture.

