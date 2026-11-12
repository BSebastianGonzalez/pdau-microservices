# API Gateway

**Source:** [pdau-api_gateway](https://github.com/BSebastianGonzalez/pdau-api_gateway)

The API Gateway is the public entry point for the frontend. It uses Spring Cloud Gateway and Eureka discovery to route requests to registered service instances instead of hard-coding every backend address.

- **Technology:** Java 21, Spring Boot, Spring Cloud Gateway, Eureka Client, Actuator.
- **Default port:** `8000`.
- **Responsibilities:** gateway routing, service discovery integration, health information, and gateway observability endpoints.
- **Downstream services:** authentication, complaint register, complaint management, complaint reports, and audit reports.

The gateway should be the only backend component exposed directly to the browser in a production topology.
