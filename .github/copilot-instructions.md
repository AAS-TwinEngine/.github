You are assisting on an open-source .NET API endpoint development project.
Default to changes that strengthen Onion Architecture, Clean Code, SOLID, security, and testability.

## Project Overview (Current Scope)

* **DataEngine**: A .NET API service aligned with **IDTA (Industrial Digital Twin Association) specifications** that dynamically generates Asset Administration Shell (AAS) structures (shell descriptors, submodels, and submodel elements).
  - On each request, DataEngine loads a template from external registries/repositories. At this time, templates contain only structure and semantic IDs; they do not contain values.
  - After DataEngine retrieves the template, it requests a plugin to provide the values needed to populate the template.
  - Once DataEngine receives the values from the plugin, it fills the template and returns a complete AAS model to the client.

### Plugin (General Concept)

A **Plugin** is a separate .NET API service that acts as the **data provider** for DataEngine.

Plugins are responsible for:

* Accessing/storing business data (database, files, or external systems).
* Resolving semantic IDs requested by DataEngine.
* Returning metadata and submodel data via JSON schema-based contracts or IDs.
* Exposing a Plugin Manifest that describes supported semantic IDs and capabilities.

### Registry & Repository (General Concept)

The AAS **registry** and **repository** services expose template and descriptor endpoints that DataEngine consumes to retrieve templates.

* These services are external platform dependencies.
* Registry/repository components are **not developed or maintained by this project**.

## Related Repositories

- **AAS.TwinEngine.DataEngine** – Core AAS orchestration API (.NET 8, Clean Architecture)
- **AAS.TwinEngine.Plugin.DPP** – Relational database data plugin (.NET 8, PostgreSQL)
- **AAS.TwinEngine.Infrastructure** – CI/CD and deployment infrastructure

## Documentation Standards

- Architecture documentation follows the **arc42** template structure (see `wiki/Architecture.md`)
- Operational procedures follow ITIL-aligned patterns (SLA, incident management, change management)
- All documentation is written in Markdown

## Conventions

- Use Markdown for all documentation
- Keep documentation concise and link to external resources (IDTA specs, BaSyx docs) rather than duplicating content

## High-Level Architecture

```mermaid
flowchart LR

    %% Users
    UIUser[UI User]
    APIUser[API User]

    %% External Access
    AASViewer["AAS Viewer (UI)"]
    APIGateway["API Gateway"]

    %% TwinEngine Components
    DataEngine["DataEngine API"]
    Plugin["Plugin API"]

    %% Plugin Storage
    PluginDB[(Plugin Database)]

    %% External AAS Infrastructure
    TemplateRegistry["AAS Template Registry (External)"]
    SubmodelRepo["AAS Submodel Template Repository (External)"]
    AASRegistry["AAS Registry (External)"]

    %% User Flows
    UIUser --> AASViewer
    APIUser --> APIGateway
    AASViewer --> APIGateway

    %% Core Communication
    APIGateway --> DataEngine
    DataEngine --> Plugin
    Plugin --> PluginDB

    %% External Registry/Repository Integration
    DataEngine --> TemplateRegistry
    DataEngine --> SubmodelRepo
    DataEngine --> AASRegistry
```

### Flow Summary

1. Clients (UI or API) send requests through the API Gateway to **DataEngine**.
2. DataEngine retrieves templates from external **AAS repositories/registries**.
3. Templates contain structure and semantic IDs but no values.
4. DataEngine requests semantic ID values from a **Plugin API** using a JSON schema structure.
5. Plugin resolves values from its database and returns them.
6. DataEngine populates the template and returns a complete AAS model to the client.

## Architecture (Onion / Clean Architecture)
- Maintain strict layer separation:
  - **DomainModel**: pure domain types only (no ASP.NET Core, no database/ADO.NET, no config/options, no logging).
  - **ApplicationLogic**: use cases, domain/application services, interfaces/ports; depends only on DomainModel.
  - **Infrastructure**: database, file system, external services; implements ApplicationLogic interfaces.
  - **Api**: HTTP/controllers/serialization; delegates to ApplicationLogic.
- Dependency direction: **Api** → **ApplicationLogic** → **DomainModel**; **Infrastructure** → **ApplicationLogic** (+ DomainModel).
- Never leak infrastructure/ASP.NET types into DomainModel/ApplicationLogic (e.g., `HttpContext`, `ControllerBase`, `DbConnection`, provider-specific types).
- Prefer defining ports (interfaces) in **ApplicationLogic** and implementing them in **Infrastructure**.

## API & DTOs
- Keep controllers thin: validation + request shaping + call handler/service + return mapped DTO.
- Do not return DomainModel types from controllers; always return DTOs under `Api/**/Responses`.
- Favor explicit request/response models over `JsonObject` when feasible; if dynamic JSON is required, isolate it to the API layer.
- Validation:
  - Validate route/query/body inputs consistently.
  - Keep validation rules close to request DTOs (or dedicated validators) and avoid duplication.

## Error Handling
- Use consistent exception mapping:
  - API should return stable error contracts (ProblemDetails or a single consistent error DTO).
  - Do not expose internal exception messages/stack traces to clients.
- Prefer centralized error handling (exception handler/middleware) over controller-level try/catch.
- Avoid using “Infrastructure”-named exceptions inside ApplicationLogic; prefer application-level exceptions and map infra exceptions at boundaries.

## Security (must be considered for any endpoint or data access)
- Assume endpoints must be protected by default.
  - Add authentication/authorization checks when introducing or modifying endpoints.
  - Prefer policy-based authorization.
- Input safety:
  - Guard against injection (SQL/command/file path) and unsafe deserialization.
  - Use parameters for database commands; never string-concatenate user inputs into SQL.
- Logging:
  - Do not log secrets, tokens, connection strings, or sensitive identifiers.
  - Use structured logging and redact sensitive fields.
- Configuration:
  - Never hard-code secrets in code or config.
  - Use environment variables/user secrets for local development; document secure setup.

## Clean Code & SOLID
- Prefer small, cohesive classes and methods.
- Watch for SRP violations (especially “handler/service” classes growing too large): extract focused collaborators.
- Prefer descriptive names; avoid abbreviations unless well-known.
- Avoid duplicate logic; introduce shared helpers only if reuse is clear.
- Follow existing patterns in this repo:
  - `Api/**/Handler/*Handler.cs` orchestrates and delegates.
  - `ApplicationLogic/Services/**` contains business/use-case logic.
  - `Infrastructure/**` contains DB execution and provider implementations.

## Coding Patterns & Style (C#/.NET)
- Match existing repo conventions: file-scoped namespaces, implicit usings, nullable enabled, primary constructors where already used.
- Async/await:
  - Async methods must end with `Async`.
  - Accept `CancellationToken cancellationToken` as the last parameter for async methods and pass it through.
  - Use `ConfigureAwait(false)` in library-style code where the repo already does.
- Guard clauses:
  - Use `ArgumentNullException.ThrowIfNull` / `ArgumentException.ThrowIfNullOrWhiteSpace` consistently at boundaries.
  - Validate inputs at the API boundary (route/query/body) and again at service boundaries when needed.
- Exceptions:
  - Throw domain/application exceptions from ApplicationLogic; map infra exceptions at boundaries.
  - Do not leak raw exception messages to clients; log details, return stable error contracts.
- Logging:
  - Use structured logging (`{PropertyName}`) and avoid logging secrets/connection strings/tokens.
  - Avoid logging decoded identifiers or payloads at `Information` unless explicitly required.
- Dependency Injection:
  - Depend on interfaces/ports from ApplicationLogic; implement them in Infrastructure.
  - Avoid static helpers for infrastructure concerns; prefer injectible collaborators.
- Formatting/readability:
  - Prefer small methods, avoid deep nesting; extract private helpers when logic branches.
  - Avoid `#region` in production code (acceptable in tests if it improves navigation).

## Testing & Quality
- Changes to business logic should be unit-testable without infrastructure.
- Add/update unit tests in `Aas.TwinEngine.Plugin.RelationalDatabase.UnitTests` for:
  - new mapping logic
  - exception mapping
  - validators and boundary conditions
- If changing architecture boundaries, ensure architecture tests remain valid.
- Prefer deterministic tests; avoid network/real DB in unit tests (use mocks).

## Unit Test Patterns (xUnit)
- Use Arrange / Act / Assert structure; keep each test focused on one behavior.
- Prefer meaningful SUT naming consistent with repo: `_sut` for system under test, `_logger` for logger substitutes, etc.
- Avoid asserting on implementation details when a behavioral assertion is available.
- Verify logging only when it is part of the behavior/contract.

### Unit Test Method Naming
Use one consistent pattern (choose the closest match to surrounding tests):
- Preferred: `{MethodUnderTest}_When{Condition}_{ReturnsOrThrows}{Expected}`
  - Example: `ExecuteQueryAsync_WhenNoRows_ThrowsResourceNotFoundException`
- Also acceptable (existing pattern): `{MethodUnderTest}_Should{Expectation}_When{Condition}`
  - Example: `DecodeBase64_ShouldThrow_OnNullOrWhitespace`
- For async tests, keep the `Async` suffix in the method under test portion of the name.

## Documentation & Open-Source Readiness
- Keep README and public docs accurate; avoid internal-only links or company-specific references.
- Public APIs should be clear and stable; add XML docs for public endpoints/types when appropriate.

## Review Output Format (when asked to review)
When asked to perform a PR/code review, categorize feedback as:
- **Must Fix**: correctness/security/architecture boundary violations
- **Should Improve**: maintainability/testability/design consistency
- **Nice to Have**: polish/ergonomics

Reference specific files and the relevant symbol/method/class names. Provide actionable steps, not just observations.
