# Service Discovery

**Source:** [pdau-service_discovery](https://github.com/BSebastianGonzalez/pdau-service_discovery)

This service runs the Netflix Eureka server used as the registry for the PDAU platform. Eureka clients register their application name and instance information, allowing the gateway and services to locate running instances dynamically.

- **Technology:** Java 21, Spring Boot, Netflix Eureka Server.
- **Default port:** `8761`.
- **Responsibilities:** service registration, instance lookup, and discovery coordination.
- **Persistence:** none; it is infrastructure rather than a business service.

It is configured not to register itself as a client or fetch a registry from another Eureka server.
