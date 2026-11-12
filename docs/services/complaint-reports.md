# Complaint Reports

**Source:** [pdau_complaint_reports](https://github.com/BSebastianGonzalez/pdau_complaint_reports)

The complaint reports service provides aggregated operational statistics for the platform.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, Eureka Client.
- **Default port:** `8000`.
- **Application name:** `creport`.
- **Responsibilities:** total complaints, distribution by state, distribution by category, and date-range reports.
- **Representative routes:** `/api/reportes/denuncias/total`, `/por-estado`, `/por-categoria`, and `/por-fechas`.

Because the gateway also declares port `8000` by default, deployment configuration must separate or override these runtime ports.
