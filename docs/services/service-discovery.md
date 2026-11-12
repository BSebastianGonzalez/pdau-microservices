# Service Discovery

**Source:** [pdau-service_discovery](https://github.com/BSebastianGonzalez/pdau-service_discovery)

This service runs the Netflix Eureka server used as the registry for the PDAU platform. Eureka clients register their application name and instance information, allowing the gateway and services to locate running instances dynamically. It is infrastructure rather than a business API.

- **Technology:** Java 21, Spring Boot, Netflix Eureka Server.
- **Default port:** `8761`.
- **Responsibilities:** service registration, instance lookup, and discovery coordination.
- **Persistence:** none; it is infrastructure rather than a business service.

It is configured not to register itself as a client or fetch a registry from another Eureka server.

## Internal structure

- `PdauServiceDiscoveryApplication` bootstraps the application.
- `@EnableEurekaServer` activates the Eureka server.
- `application.properties` disables self-registration and registry fetching, making this process the discovery authority for the local platform.

There are no domain controllers, entities, or repositories because the service manages the service registry rather than PDAU business data.

## Main flow

1. The discovery server starts on port `8761`.
2. Authentication, complaint, management, reporting, and gateway applications start as Eureka clients.
3. Each client registers its application name, host, port, and instance ID.
4. The gateway queries Eureka to resolve a target service.
5. Eureka returns a healthy registered instance and the gateway forwards the request.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau-service_discovery.git
cd pdau-service_discovery
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. No database or broker is required. The server runs on `8761`, and client services should use its Eureka URL as `SERVICE_DISCOVERY`.
