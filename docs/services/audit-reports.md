# Audit Reports

**Source:** [pdau_audit_reports](https://github.com/BSebastianGonzalez/pdau_audit_reports)

The audit reports service exposes historical activity information for administrator actions across the complaint workflow.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, Eureka Client.
- **Default port:** `3001`.
- **Application name:** `areport`.
- **Responsibilities:** global activity totals, date-range totals, administrator-specific totals, and breakdowns for responses, archiving, comments, and appeal responses.
- **Representative routes:** `/totales`, `/totales/rango`, `/admin/{adminId}/totales`, `/admin/{adminId}/respuestas`, `/admin/{adminId}/archivamientos`, `/admin/{adminId}/comentarios`, and `/apelaciones`.

The service is intended for traceability and historical analysis rather than primary complaint management.
