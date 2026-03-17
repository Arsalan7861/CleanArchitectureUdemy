# CleanArchitecture.Udemy

A sample **ASP.NET Core Web API** built with **Clean Architecture**, **CQRS**, **MediatR**, **Entity Framework Core**, **ASP.NET Core Identity**, and **JWT authentication**.

This solution separates concerns into distinct layers for domain logic, application use cases, infrastructure concerns, persistence, presentation, and hosting.

## Features

- Clean Architecture layering
- CQRS with MediatR
- FluentValidation pipeline behavior
- JWT authentication with refresh token support
- ASP.NET Core Identity based user and role management
- Custom role-based endpoint filtering
- Entity Framework Core with SQL Server
- Serilog logging to:
  - Console
  - Rolling file logs
  - SQL Server
- Swagger / OpenAPI support in development
- xUnit + Moq unit test project

## Solution Structure

- **CleanArchitecture.Domain**  
  Core entities, DTOs, abstractions, and repository contracts.

- **CleanArchitecture.Application**  
  Commands, queries, validators, handlers, service contracts, and pipeline behaviors.

- **CleanArchitecture.Persistance**  
  EF Core `DbContext`, mappings, repositories, services, and migrations.

- **CleanArcihtecture.Infrastructure**  
  JWT provider, authorization filters, and infrastructure services.

- **CleanArchitecture.Presentation**  
  API controllers and base API controller abstraction.

- **CleanArchitecture.WebApi**  
  Application entry point, dependency injection, middleware, Swagger, and configuration.

- **CleanArchitecture.UnitTest**  
  Unit tests for controller behavior.

## Architecture Overview

The project follows a layered structure:

- **Domain** contains business entities such as `User`, `Role`, `UserRole`, `Car`, and `ErrorLog`.
- **Application** defines use cases such as:
  - user registration
  - login
  - refresh token creation
  - role creation
  - user-role assignment
  - car creation
  - paginated car listing
- **Persistence** implements data access using EF Core and SQL Server.
- **Infrastructure** provides JWT token generation and custom authorization behavior.
- **Presentation** exposes the API via controllers.
- **WebApi** wires everything together and hosts the application.

## Tech Stack

- .NET 9
- ASP.NET Core Web API
- MediatR
- FluentValidation
- Entity Framework Core
- SQL Server
- ASP.NET Core Identity
- JWT Bearer Authentication
- AutoMapper
- Serilog
- Swagger
- xUnit
- Moq

## Domain Models

Main entities used in the project:

- **User**
- **Role**
- **UserRole**
- **Car**
- **ErrorLog**

`Car` includes:
- `Name`
- `Model`
- `EnginePower`

## API Conventions

The base controller route is:

- `api/[controller]`

Controller actions use `[HttpPost("[action]")]`, so endpoint names are generated from the action name.

Example:
- `POST /api/Auth/Login`
- `POST /api/Cars/GetAll`

All controllers inherit from a base controller with JWT bearer authorization enabled by default. Anonymous access is explicitly allowed only where configured.

## Available Endpoints

### Auth

- `POST /api/Auth/Register`
- `POST /api/Auth/Login`
- `POST /api/Auth/CreateTokenByRefreshToken`

#### Register request

```json
{
  "email": "user@example.com",
  "userName": "demo",
  "nameLastName": "Demo User",
  "password": "123456"
}
```

#### Login request

```json
{
  "userNameOrEmail": "demo",
  "password": "123456"
}
```

### Roles

- `POST /api/Roles/Create`

```json
{
  "name": "Create"
}
```

### UserRoles

- `POST /api/UserRoles/Create`

```json
{
  "roleId": "role-id",
  "userId": "user-id"
}
```

### Cars

- `POST /api/Cars/Create`
- `POST /api/Cars/GetAll`

#### Create car request

```json
{
  "name": "Toyota",
  "model": "Corolla",
  "enginePower": 130
}
```

#### Get all cars request

```json
{
  "pageNumber": 1,
  "pageSize": 10,
  "search": "toyota"
}
```

## Authorization

JWT bearer authentication is used for protected endpoints.

Anonymous access is allowed for:
- `Register`
- `Login`

Car endpoints also use a custom role filter:
- `Cars/Create` requires role name: `Create`
- `Cars/GetAll` requires role name: `GetAll`

A typical flow is:

1. Register a user
2. Login and get a JWT token
3. Create roles such as `Create` and `GetAll`
4. Assign a role to a user
5. Call protected car endpoints with the bearer token

## Validation and Error Handling

- FluentValidation is registered through a MediatR pipeline behavior.
- Unhandled exceptions are captured by custom middleware.
- Exceptions are logged into the database and returned as JSON responses.
- Validation failures are returned through the same middleware pipeline.

## Logging

Serilog is configured to write logs to:

- Console
- `Logs/log-.txt`
- SQL Server

The application also stores exception details in the database through the `ErrorLog` entity.

## Configuration

Main configuration lives in:

- `CleanArchitecture.WebApi/appsettings.json`

Important settings:

- `ConnectionStrings:SqlServer`
- `Jwt:Issuer`
- `Jwt:Audience`
- `Jwt:SecretKey`

Before running the project, update these values for your environment.

## Getting Started

### Prerequisites

- .NET 9 SDK
- SQL Server
- Visual Studio 2026 or newer / VS Code / CLI

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd CleanArchitecture.Udemy
```

### 2. Update configuration

Edit:

- `CleanArchitecture.WebApi/appsettings.json`

Set:

- a valid SQL Server connection string
- secure JWT settings

### 3. Restore dependencies

```bash
dotnet restore
```

### 4. Apply database migrations

```bash
dotnet ef database update --project CleanArchitecture.Persistance --startup-project CleanArchitecture.WebApi
```

### 5. Run the API

```bash
dotnet run --project CleanArchitecture.WebApi
```

### 6. Open Swagger

When running in development, Swagger UI is available from the application base URL.

## Testing

Run tests with:

```bash
dotnet test
```

The solution currently includes a unit test project for controller-level behavior using:

- xUnit
- Moq

## Notes

- The project uses SQL Server and ASP.NET Core Identity together in the persistence layer.
- Controllers delegate work to MediatR rather than containing business logic.
- The current API uses `POST` for query-style endpoints such as `Cars/GetAll` to accept complex request models in the request body.

## License

This project is provided for educational and learning purposes.