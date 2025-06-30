POSTMAN COLLECTION - https://drive.google.com/file/d/1ErHQaNppJrYdJ9pn0Y0-9gfAK9lPuO21/view?usp=sharing

# Eureka Discovery Service

📌 Overview
This application is a Eureka Server that acts as a service registry in a microservices architecture. It allows microservices to dynamically register and discover each other without hard-coded hostnames and ports.

🚀 Key Features
- Service registration and discovery
- Secure access to Eureka dashboard and REST endpoints
- Actuator health/info monitoring
- Optional integration with Spring Cloud Config Server

🧰 Prerequisites
- Java 17+
- Spring Boot 3.x
- Spring Cloud Netflix Eureka Server
- (Optional) Spring Cloud Config Client

🔐 Authentication
- Eureka dashboard (`/eureka/**`): HTTP Basic Auth (username/password)
- Health/Info endpoints (`/actuator/*`): Open (no auth)

---

🌐 API Endpoints

1. **Eureka Dashboard and API**
- Method: `GET`
- Endpoint: `/eureka/**`
- Auth: HTTP Basic
- Description: Access Eureka dashboard and registry info

✅ Example:
curl -u admin:securepassword http://localhost:8761/eureka/

➡ Response (200 OK):
HTML of the Eureka dashboard

❌ Unauthorized:
curl http://localhost:8761/eureka/
→ HTTP/1.1 401 Unauthorized

---

2. **Health Check**
- Method: `GET`
- Endpoint: `/actuator/health`
- Auth: None

✅ Example:
curl http://localhost:8761/actuator/health

➡ Response:
{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 500107862016,
        "free": 350107862016,
        "threshold": 10485760,
        "exists": true
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}

---

3. **Application Info**
- Method: `GET`
- Endpoint: `/actuator/info`
- Auth: None

✅ Example:
curl http://localhost:8761/actuator/info

➡ Response:
{
  "app": {
    "name": "discovery",
    "version": "1.0.0"
  }
}

---

⚙️ How It Works

✅ **Service Registration**  
Microservices register themselves with Eureka on startup using `@EnableEurekaClient`.

✅ **Service Discovery**  
Clients query Eureka to find instances of other services.

✅ **Heartbeats**  
Services send heartbeats to stay registered.

✅ **Load Balancing**  
Clients can use discovery with Ribbon or other load balancers.

---

🔧 Configuration (YAML)

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
  security:
    username: admin
    password: securepassword
management:
  endpoints:
    web:
      exposure:
        include: health,info

server:
  port: 8761
spring:
  application:
    name: discovery
  security:
    user:
      name: admin
      password: securepassword
  cloud:
    config:
      uri: http://localhost:8888
      name: discovery-service

---

🧪 Running the Application

With Maven:
mvn spring-boot:run

Or with packaged JAR:
java -jar discovery-service.jar

If using external config:
- Ensure Config Server is up before Eureka starts

---

📦 Dependencies

- spring-boot-starter-web  
- spring-cloud-starter-netflix-eureka-server  
- spring-boot-starter-security  
- spring-boot-starter-actuator  
- spring-cloud-starter-config (optional)

---

🔐 Security Considerations

- CSRF disabled for `/eureka/**` to allow service registration
- Eureka dashboard is protected via HTTP Basic Auth
- Health endpoints (`/actuator/*`) remain publicly accessible
- Passwords can be encrypted with BCrypt in config server
