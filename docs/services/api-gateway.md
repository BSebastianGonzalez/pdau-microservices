# API Gateway

**Source:** [pdau-api_gateway](https://github.com/BSebastianGonzalez/pdau-api_gateway)

The API Gateway is the public entry point for the frontend. It uses Spring Cloud Gateway and Eureka discovery to route requests to registered service instances instead of hard-coding every backend address. This gives the browser one stable backend origin while the internal services can be moved or scaled independently.

- **Technology:** Java 21, Spring Boot, Spring Cloud Gateway, Eureka Client, Actuator.
- **Default port:** `8000`.
- **Responsibilities:** gateway routing, service discovery integration, health information, and gateway observability endpoints.
- **Downstream services:** authentication, complaint register, complaint management, complaint reports, and audit reports.

The gateway should be the only backend component exposed directly to the browser in a production topology.

## Internal structure

- `PdauApiGatewayApplication` bootstraps the reactive Spring Cloud Gateway application.
- `CorsConfig` configures cross-origin behavior for frontend clients.
- Spring Cloud Gateway's discovery locator creates routes from service IDs registered in Eureka and converts them to lower-case service identifiers.
- Actuator exposes health, information, and gateway inspection endpoints.

There are no business entities or JPA repositories in this component because it does not own domain data.

## API guide

The gateway does not declare business controllers. Its API is the dynamically discovered API of the registered services, exposed through the gateway's service routes. The exact public path depends on the service ID registered in Eureka and the gateway discovery-locator configuration.

In addition, Actuator exposes operational endpoints:

| Method | Endpoint | Function |
|---|---|---|
| `GET` | `/actuator/health` | Checks gateway health |
| `GET` | `/actuator/info` | Returns application information |
| `GET` | `/actuator/gateway` | Inspects gateway routes and configuration |

The gateway forwards the business endpoints documented in the other service guides. It should not be used as a replacement for their API contracts.

## Main flow

1. The frontend sends an HTTP request to the gateway.
2. The gateway resolves the target service through Eureka.
3. Discovery-based routing forwards the request to a registered instance.
4. The downstream service processes the business operation and returns a response.
5. The gateway relays the response to the frontend.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau-api_gateway.git
cd pdau-api_gateway
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. Configure `SERVICE_DISCOVERY`. The application name is `pdau-api-gateway` and the default port is `8000`. Start Eureka before the gateway.
