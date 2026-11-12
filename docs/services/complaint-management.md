# Complaint Management

**Source:** [pdau-complaint_management](https://github.com/BSebastianGonzalez/pdau-complaint_management)

The complaint management service supports the administrative lifecycle after a complaint has been registered.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, RabbitMQ, SendGrid, Cloudinary, Eureka Client.
- **Default port:** `3000`.
- **Application name:** `management`.
- **Responsibilities:** responses, response attachments, comments, appeals, appeal responses, archiving and unarchiving, and management indicators.
- **Representative routes:** `/api/respuestas`, `/api/comentarios`, `/api/apelaciones`, `/api/respuestas-apelacion`, `/api/archivar`, and `/api/indicadores`.

The service integrates with the registration and authentication services through configured service URLs, and supports notification and file-storage integrations.
