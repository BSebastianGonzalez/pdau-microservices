# Audit Reports

**Source:** [pdau_audit_reports](https://github.com/BSebastianGonzalez/pdau_audit_reports)

The audit reports service exposes historical activity information for administrator actions across the complaint workflow. It is an append-oriented audit projection: it receives activity events from management and stores queryable records for traceability and analysis.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, Eureka Client.
- **Default port:** `3001`.
- **Application name:** `areport`.
- **Responsibilities:** global activity totals, date-range totals, administrator-specific totals, and breakdowns for responses, archiving, comments, and appeal responses.
- **Representative routes:** `/totales`, `/totales/rango`, `/admin/{adminId}/totales`, `/admin/{adminId}/respuestas`, `/admin/{adminId}/archivamientos`, `/admin/{adminId}/comentarios`, and `/apelaciones`.

The service is intended for traceability and historical analysis rather than primary complaint management.

## Internal structure

- `AuditoriaRespuestaListener`, `AuditoriaComentarioListener`, `AuditoriaApelacionListener`, and `AuditoriaArchivamientoListener` consume the corresponding RabbitMQ events.
- `AuditoriaService` coordinates audit queries and administrator-specific aggregations.
- `AuditoriaController` exposes global, date-range, administrator, and activity-type endpoints.
- `AuditoriaRespuesta`, `AuditoriaComentario`, `AuditoriaApelacion`, `AuditoriaArchivamiento`, and `AuditoriaRespuestaApelacion` persist independent audit records.
- `TotalesDTO` and `AdminTotalesDTO` shape aggregated responses.
- Each audit repository isolates persistence for its activity type.

## Main flow

1. The management service performs an auditable action such as responding, commenting, appealing, or archiving.
2. The corresponding audit event is published through RabbitMQ.
3. The specialized listener receives the event and writes an audit entity using its repository.
4. An administrator requests totals or filters by date, administrator, or action type.
5. `AuditoriaService` aggregates the stored records and returns DTOs through `AuditoriaController`.

The service deliberately stores audit information independently from the operational management models, preserving a historical view even when operational records change.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau_audit_reports.git
cd pdau_audit_reports
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. Configure `DB_URL`, `DB_USER`, `DB_PASSWORD`, `RABBITMQ_URL`, and `SERVICE_DISCOVERY`. The application name is `areport` and the default port is `3001`.
