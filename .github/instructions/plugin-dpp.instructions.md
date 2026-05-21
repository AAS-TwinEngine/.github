```yaml
---
description: "Use when working on Plugin.DPP — the relational database plugin providing data values for AAS submodel templates"
applyTo: "AAS.TwinEngine.Plugin.RelationalDatabase/**"
---
```

# Project Guidelines

## Overview

AAS.TwinEngine.Plugin.DPP is a data plugin for the Digital Product Passport (DPP). It provides actual data values for requested Semantic IDs from a relational database. This plugin is consumed by the DataEngine to populate AAS submodel templates with real data.

## Tech Stack

- .NET 8.0 / C# / ASP.NET Core
- PostgreSQL via Npgsql (native JSON query support)
- OpenTelemetry for distributed tracing
- Serilog for structured logging
- NSwag for OpenAPI/Swagger generation
- JsonSchema.Net for schema-driven API contracts
- Docker (Linux containers)

## Architecture

This project follows **Clean Architecture** consistent with the DataEngine:

- **Api/** – REST controllers and endpoints
- **ApplicationLogic/** – Business logic and service layer
- **DomainModel/** – Domain entities and value objects
- **Infrastructure/** – Database access layer with embedded SQL queries
- **ServiceConfiguration/** – Dependency injection configuration

Dependencies point inward. Architecture rules are enforced by `TngTech.ArchUnitNET` tests.

## Supported DPP Submodels

- Nameplate v3.0.1
- ContactInformation v1.0
- HandoverDocumentation v2.0.1
- TechnicalData v1.2.1
- CarbonFootprint v1.0.1

## Code Style

- Nullable reference types are enabled
- Implicit usings are enabled
- .NET analyzers are active
- SQL query files are embedded as resources in the output
- API versioning via headers using `Asp.Versioning`

## Build and Test

```bash
dotnet build source/AAS.TwinEngine.Plugin.RelationalDatabase.sln
dotnet test source/AAS.TwinEngine.Plugin.RelationalDatabase.sln
```

- **xUnit** for unit tests
- **NSubstitute** for mocking – do not use Moq
- **Coverlet** for code coverage (see `coverlet.runsettings`)
- Architecture tests validate Clean Architecture boundaries via ArchUnitNET

## Conventions

- Communication with DataEngine is schema-driven via JSON Schema contracts
- Semantic ID mapping links AAS identifiers to database columns/values
- SQL queries are stored as embedded resources within the Infrastructure layer
- Database schema definitions are in `example/postgres/` (numbered `.sql.inc` files loaded by `init.sql`)
- Plugin acts as a data-only layer – no AAS logic belongs here
