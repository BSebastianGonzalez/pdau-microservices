# Complaint Management

**Source:** [pdau-complaint_management](https://github.com/BSebastianGonzalez/pdau-complaint_management)

The complaint management service supports the administrative lifecycle after a complaint has been registered. It is the operational core used to review a case, communicate a response, receive comments and appeals, archive records, and generate management indicators.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, SendGrid, Cloudinary, Eureka Client.
- **Default port:** `3000`.
- **Application name:** `management`.
- **Responsibilities:** responses, response attachments, comments, appeals, appeal responses, archiving and unarchiving, and management indicators.
- **Representative routes:** `/api/respuestas`, `/api/comentarios`, `/api/apelaciones`, `/api/respuestas-apelacion`, `/api/archivar`, and `/api/indicadores`.

The service integrates with the registration and authentication services through configured service URLs, and supports notification and file-storage integrations.

## Internal structure

- Controllers expose separate resources for responses, comments, appeals, appeal responses, archiving, and indicators.
- `RespuestaService` and `RespuestaApelacionService` manage ordinary and appeal responses.
- `ComentarioService` manages administrator comments associated with a complaint.
- `ApelacionService` enforces the appeal flow and its configured time window.
- `ArchivarDenunciaService` handles archive and unarchive operations.
- `IndicadorGestionService` generates and reads management indicators.
- `EmailService` sends workflow notifications.
- `CloudinaryService` handles uploaded response and appeal files.
- JPA models represent responses, comments, appeals, attachments, archive records, and indicators.
- Audit event classes (`RespuestaAuditEvent`, `ComentarioAuditEvent`, `ApelacionAuditEvent`, `ArchivamientoAuditEvent`, and related events) publish activity for the audit service.
- RabbitMQ configuration supports asynchronous coordination with the register, reports, and audit services.

## Main flow

1. An administrator selects a complaint already registered by the intake service.
2. The management API stores a response, comment, appeal, or archive action in its own database.
3. Attachments are uploaded to the configured Cloudinary account and their metadata is persisted locally.
4. The service sends notifications when the workflow requires it and publishes an audit event through RabbitMQ.
5. The register and audit/reporting consumers process the event without direct access to the management database.
6. A complainant can later consult the response or submit an appeal using the tracking flow exposed by the frontend.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau-complaint_management.git
cd pdau-complaint_management
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. Configure `DB_URL`, `DB_USER`, `DB_PASSWORD`, `RABBITMQ_URL`, `SERVICE_DISCOVERY`, `REGISTRO_DENUNCIAS_URL`, `AUTHENTICATION_URL`, SendGrid variables, and Cloudinary variables. The application name is `management` and the default port is `3000`.
