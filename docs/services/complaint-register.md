# Complaint Register

**Source:** [pdau_complaint_register](https://github.com/BSebastianGonzalez/pdau_complaint_register)

The complaint register is the intake service for anonymous reports. It stores the initial complaint information and supporting evidence, and exposes the catalog data needed by the registration flow.

- **Technology:** Java 21, Spring Boot, JPA, MySQL, Cloudinary, RabbitMQ, Eureka Client.
- **Default port:** `8080`.
- **Application name:** `complaint`.
- **Responsibilities:** create and consult complaints, retrieve complaints by tracking token, manage categories and states, and attach evidence.
- **Representative routes:** `/api/denuncias`, `/api/evidencia`, `/api/categorias`, and `/api/estados`.

The service publishes or consumes RabbitMQ events and uses environment variables for database, broker, and Eureka connectivity.
