# Complaint Reports

**Source:** [pdau_complaint_reports](https://github.com/BSebastianGonzalez/pdau_complaint_reports)

The complaint reports service provides aggregated operational statistics for the platform without querying the complaint register database directly. It maintains a reporting projection based on events and exposes read-oriented endpoints for dashboards.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, Eureka Client.
- **Default port:** `8000`.
- **Application name:** `creport`.
- **Responsibilities:** total complaints, distribution by state, distribution by category, and date-range reports.
- **Representative routes:** `/api/reportes/denuncias/total`, `/por-estado`, `/por-categoria`, and `/por-fechas`.

Because the gateway also declares port `8000` by default, deployment configuration must separate or override these runtime ports.

## Internal structure

- `DenunciaEventsListener` consumes complaint-created and complaint-state-updated events from RabbitMQ.
- `ReporteDenunciaService` applies event data to the reporting model and executes aggregation queries.
- `ReporteDenunciaController` exposes the statistics API.
- `ReporteDenuncia` is the reporting JPA entity.
- `ReporteDenunciaRepository` provides persistence and query operations for the projection.
- Event DTOs describe the minimum data needed to update the projection.

## Entities

| Entity | Purpose |
|---|---|
| `ReporteDenuncia` | Local projection of complaint data used for aggregations by state, category, and date |

The projection is populated from events rather than by querying the complaint register database.

## API guide

Base URL when accessed directly: `http://localhost:8000`.

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `GET` | `/api/reportes/denuncias/total` | — | Returns the total number of complaints |
| `GET` | `/api/reportes/denuncias/por-estado` | — | Returns a map with complaint counts grouped by state |
| `GET` | `/api/reportes/denuncias/por-categoria` | — | Returns a map with complaint counts grouped by category |
| `GET` | `/api/reportes/denuncias/por-fechas` | Query `inicio` and `fin` as ISO dates (`YYYY-MM-DD`) | Returns the number of complaints created within a date range |

## Main flow

1. The register service publishes an event when a complaint is created or its state changes.
2. `DenunciaEventsListener` receives the event through RabbitMQ.
3. `ReporteDenunciaService` creates or updates the local `ReporteDenuncia` projection.
4. An administrator dashboard requests totals, state/category distributions, or date-range results.
5. `ReporteDenunciaController` serves the result from the reporting database.

This event-driven projection keeps reporting queries independent from the transactional complaint database.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau_complaint_reports.git
cd pdau_complaint_reports
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. Configure `DB_URL`, `DB_USER`, `DB_PASSWORD`, `RABBITMQ_URL`, and `SERVICE_DISCOVERY`. The application name is `creport`; its default port is `8000`, which should be changed when the gateway uses that port on the same host.
