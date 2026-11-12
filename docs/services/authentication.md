# Authentication

**Source:** [pdau_authentication](https://github.com/BSebastianGonzalez/pdau_authentication)

The authentication service is the identity boundary for the administrative side of PDAU. It validates credentials, issues JWT-based authentication responses, manages administrator profiles, and supports password recovery. It also stores administrator documents used by the platform.

- **Technology:** Java 21, Spring Boot, Spring Security, JPA, JWT, MySQL, SendGrid, Thymeleaf, Eureka Client.
- **Default port:** `8081`.
- **Application name:** `authentication`.
- **Responsibilities:** login, password recovery and reset, administrator CRUD operations, special email queries, and evaluator/administrator document management.
- **Representative routes:** `/auth/login`, `/auth/forgot-password`, `/auth/reset-password`, and `/api/admin`.

## Internal structure

- `AuthController` exposes login and password recovery endpoints.
- `AuthService` coordinates credential validation and authentication responses.
- `JwtService` creates and validates JWT data.
- `JwtFilter` extracts the bearer token from requests and integrates it with Spring Security.
- `SecurityConfig` defines the security filter chain and endpoint access rules.
- `AdminController` exposes administrator and document operations.
- `AdminService` contains administrator business rules.
- `PasswordResetService` manages reset tokens and recovery flow.
- `Admin`, `Documento`, and `PasswordResetToken` are the main JPA entities.
- Repositories persist administrators, documents, and reset tokens in the service-owned MySQL database.

## Entities

| Entity | Purpose |
|---|---|
| `Admin` | Administrator identity, contact data, credentials, and role |
| `Documento` | File associated with an administrator, including name, content type, and binary data |
| `PasswordResetToken` | Temporary token used during password recovery |
| `Rol` | Administrator authorization role |
| `TipoDocumento` | Supported administrator document categories |

## API guide

Base URL when accessed directly: `http://localhost:8081`.

### Authentication

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/auth/login` | JSON `AuthRequest` with `correo` and `contrasenia` | Validates credentials and returns `AuthResponse` with authentication data |
| `POST` | `/auth/forgot-password` | JSON with `correo` | Sends a password recovery link |
| `POST` | `/auth/reset-password` | JSON `ResetPasswordRequest` with `token` and `newPassword` | Changes the password using a valid recovery token |

### Administrators

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/admin` | JSON `Admin` | Creates an administrator |
| `GET` | `/api/admin` | — | Lists administrators |
| `GET` | `/api/admin/{id}` | Path `id` | Gets one administrator |
| `PUT` | `/api/admin/{id}` | Path `id`, JSON `Admin` | Updates an administrator |
| `GET` | `/api/admin/especiales/correos` | — | Gets the email addresses of special administrators |

### Administrator documents

These endpoints use `multipart/form-data` or a document query parameter `tipo` based on the `TipoDocumento` enum.

| Method | Endpoint | Body or parameters | Function |
|---|---|---|---|
| `POST` | `/api/admin/{id}/documento` | `file`, `tipo` | Uploads a document |
| `GET` | `/api/admin/{id}/documento` | Query `tipo` | Downloads the document with its stored content type |
| `PUT` | `/api/admin/{id}/documento` | `file`, `tipo` | Replaces an existing document |
| `DELETE` | `/api/admin/{id}/documento` | Query `tipo` | Deletes a document |

## Main flow

1. An administrator submits credentials to `/auth/login`.
2. `AuthService` retrieves the administrator and compares the stored password using the configured encoder.
3. `JwtService` creates the token returned in `AuthResponse`.
4. Later protected requests pass through `JwtFilter` before reaching administrator endpoints.
5. Password recovery creates a `PasswordResetToken`, sends a recovery message, and validates the token before changing the password.

## Installation

```bash
git clone https://github.com/BSebastianGonzalez/pdau_authentication.git
cd pdau_authentication
./mvnw spring-boot:run
```

On Windows use `mvnw.cmd spring-boot:run`. Configure `DB_URL`, `DB_USER`, `DB_PASSWORD`, `SENDGRID_API_KEY`, and `SERVICE_DISCOVERY`. The service registers with Eureka using the `authentication` application name and listens on port `8081` by default.
