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

## Entities

| Entity | Purpose |
|---|---|
| `Respuesta` | Administrative response to a complaint, including author, detail, date, and attachments |
| `ArchivoRespuesta` | File attached to an administrative response |
| `Comentario` | Internal or workflow comment associated with a complaint |
| `Apelacion` | Appeal submitted for a complaint, including its files and result |
| `ArchivoApelacion` | File attached to an appeal |
| `RespuestaApelacion` | Administrative answer to an appeal |
| `ArchivoRespuestaApelacion` | File attached to an appeal response |
| `ArchivamientoDenuncia` | Archive/unarchive record, administrator, justification, and reason |
| `IndicadorGestion` | Generated management indicator |

The service stores foreign identifiers such as `denunciaId` and `adminId`; it does not share the authentication or register service databases.

## API guide

Base URL when accessed directly: `http://localhost:3000`.

### Responses

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/respuestas` | Multipart `denunciaId`, `adminId`, `detalle`, optional `files` | Creates an administrative response |
| `GET` | `/api/respuestas/{denunciaId}` | Path `denunciaId` | Gets the response for a complaint |
| `GET` | `/api/respuestas/{respuestaId}/archivos` | Path `respuestaId` | Lists response files and visualization URLs |

### Comments

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/comentarios` | JSON `Comentario` | Creates a comment |
| `GET` | `/api/comentarios/denuncia/{idDenuncia}` | Path `idDenuncia` | Lists comments for a complaint |
| `DELETE` | `/api/comentarios/{id}` | Path `id` | Deletes a comment |

### Appeals and appeal responses

These operations use `multipart/form-data`; the `data` part contains the corresponding DTO and `files` is optional.

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/apelaciones` | Part `data` as `ApelacionDTO`, optional `files` | Creates an appeal |
| `GET` | `/api/apelaciones/denuncia/{denunciaId}` | Path `denunciaId` | Gets the appeal for a complaint |
| `POST` | `/api/respuestas-apelacion` | Part `data` as `RespuestaApelacionDTO`, optional `files` | Answers an appeal |
| `GET` | `/api/respuestas-apelacion/denuncia/{denunciaId}` | Path `denunciaId` | Gets the appeal response for a complaint |

### Archive and unarchive

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/archivar` | Query `denunciaId`, `adminId`, `justificacion` | Archives a complaint |
| `GET` | `/api/archivar` | — | Lists archive records |
| `GET` | `/api/archivar/denuncia/{denunciaId}` | Path `denunciaId` | Gets the archive record for a complaint |
| `PUT` | `/api/archivar/{denunciaId}/desarchivar` | Query `adminId`, `motivo` | Reopens an archived complaint |

### Management indicators

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/indicadores/generar` | — | Generates a new management indicator |
| `GET` | `/api/indicadores` | — | Lists generated indicators |
| `GET` | `/api/indicadores/{id}` | Path `id` | Gets an indicator |

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
