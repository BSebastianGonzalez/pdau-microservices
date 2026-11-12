# Authentication

**Source:** [pdau_authentication](https://github.com/BSebastianGonzalez/pdau_authentication)

The authentication service protects administrative workflows and manages administrator-related data.

- **Technology:** Java 21, Spring Boot, Spring Security, JPA, JWT, MySQL, SendGrid, Thymeleaf, Eureka Client.
- **Default port:** `8081`.
- **Application name:** `authentication`.
- **Responsibilities:** login, password recovery and reset, administrator CRUD operations, special email queries, and evaluator/administrator document management.
- **Representative routes:** `/auth/login`, `/auth/forgot-password`, `/auth/reset-password`, and `/api/admin`.

The service is configured with database, SendGrid, and Eureka environment variables.
