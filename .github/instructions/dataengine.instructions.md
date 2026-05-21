```yaml
---
description: "Use when working on DataEngine — the AAS orchestration API that combines templates with plugin data"
applyTo: "AAS.TwinEngine.DataEngine/**"
---
```

# Project Guidelines

## Overview

AAS.TwinEngine.DataEngine is the core orchestration layer for the Asset Administration Shell (AAS) platform. It dynamically generates complete AAS submodels by combining standardized templates with runtime data from plugins. It integrates with Eclipse BaSyx components and follows IDTA-aligned REST API specifications.

## Tech Stack

- .NET 8.0 / C# / ASP.NET Core
- MongoDB (template storage)
- OpenTelemetry for distributed tracing
- Serilog for structured logging
- NSwag for OpenAPI/Swagger generation
- Polly for resilience/retry policies
- AasCore.Aas3 for AAS package support
- Docker (Linux containers)

## Architecture

This project follows **Clean Architecture** with strict layer separation:

- **Api/** – REST controllers, organized by AAS subdomain (`AasRegistry/`, `AasRepository/`, `SubmodelRegistry/`, `SubmodelRepository/`)
- **ApplicationLogic/** – Business logic, services, exception handling
- **DomainModel/** – Core domain models and entities, organized by subdomain and plugin models
- **Infrastructure/** – Data access, HTTP providers, monitoring, plugin data provider
- **Authorization/** – Security (placeholder for future implementation)
- **ServiceConfiguration/** – Dependency injection, CORS, logging, and health check configuration

Dependencies point inward: Api → ApplicationLogic → DomainModel ← Infrastructure. Architecture rules are enforced by `TngTech.ArchUnitNET` tests.

## Code Style

- Nullable reference types are enabled – handle nullability explicitly
- Implicit usings are enabled
- .NET analyzers are active – resolve all warnings
- Use async/await consistently with proper naming (`Async` suffix)
- API versioning is done via headers using `Asp.Versioning`

## Build and Test

```bash
dotnet build source/AAS.TwinEngine.DataEngine.sln
dotnet test source/AAS.TwinEngine.DataEngine.sln
```

- **xUnit** for unit and module tests
- **NSubstitute** for mocking – do not use Moq
- **Coverlet** for code coverage (see `coverlet.runsettings`)
- Test projects: `AAS.TwinEngine.DataEngine.UnitTests/`, `AAS.TwinEngine.DataEngine.ModuleTests/`
- Architecture tests validate Clean Architecture boundaries via ArchUnitNET

## Conventions

- Plugin communication is schema-driven via JSON Schema contracts (`JsonSchema.Net`)
- Template-to-data mapping uses Semantic IDs with regex-based pattern matching
- Multi-plugin conflict handling supports modes: `TakeFirst`, `SkipConflictingIds`, `ThrowError`
- HTTP providers use Polly retry policies – configure via `appsettings.json`
- Dependency injection extensions are split by layer: `ApplicationDependencyInjectionExtensions`, `InfrastructureDependencyInjectionExtensions`
- OpenAPI spec is auto-generated via NSwag (`config.nswag`)
