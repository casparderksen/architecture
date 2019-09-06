# About

This project documents what I know and believe in about Architecture under an Open Source license.

# Blog

# Principles 

# Patterns

# Architecture

* [Technology](architecture/technology)

# Architecture Decisions

This section documents generic Architecture Decisions that should apply to any software project.
This is a first brain dump.

An 'Architecture Decision' is a design decision that addresses a functional or non-functional requirement
that is architecturally significant. Decisions are architecturally significant if they are hard to change
afterwards. This may include technology selection and library choices. 

## Documentation 

Architecture decisions are recorded as [MADR](https://adr.github.io/madr/) documents in version control. 
TODO: refactor this document to MADR records.

## Code organization

The application is organized according to
[Onion Architecture](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/) principles
(similar to Hexagonal or Clean Architecture).
This means that the code is organized into independent functional slices. Each slice is organized into layers.
Dependencies between layers point from concrete to more abstract. Stable concepts such as the business domain
may not depend on less stable things such as infrastructure or other applications. Abstractions may not depend 
on implementations. Communications with external systems and infrastructure must be encapsulated in adapters.
Adapters must be independent of each other.

Layers and package structure:
- `org.my.app.<slice>`: slice of functionality (not a layer)
  - `domain`: core logic, depends on business domain, framework-free
    - `model`: domain entities (behaviour and encapsulated data)
    - `service`: domain services
  - `application`: application services or use cases (application specific workflow)
  - `adapter`: adapters for external systems or infrastructure
    - `rest`: REST API (thin, no logic other than DTO mapping and building responses)
- `org.my.util.<slice>`: application independent generic logic (could be moved to library)

## Dependency rules

- Slices must be independent
- There must not be cyclic dependencies
- The `domain` package may not depend on other packages
- The `application` package may depend on `domain`, but not on any `adapter`
- An `adapter` package may use `application` and `domain`
- An `adapter` package may not depend on any other `adapter` package
- A `util` package may not depend on `app` packages

Consequences:
- Adapters may not orchestrate use cases, delegate to an application service instead.
- Use application services to send data from an input adapter to an output adapter (workflow).
- Use Dependency Inversion or domain events for invoking adapters from domain or application services.

## Validating architecture rules

Use [ArchUnit](https://www.archunit.org) unit tests for validating compliance to these rules.

## Integrity of models

We want domain and JPA entities to be consistent and valid at all times.
Therefore, REST adapters use DTOs as separate input and output models.
DTOs and JAXs parameters must always be validated.

## Java frameworks for data mapping

The following frameworks for data mapping are used: 
- JAXB for XML to/from object mapping (implicit with JAX-RS)
- JSON-B for JSON to/from object mapping (implicit with JAX-RS)
- JPA for object-relational mapping
- MapStruct for object-to-object mapping (model-2-model)
Rationale: low/or no coding required. MapStruct is a compiler- time framework.

## Clean Architecture Principles

- Testable: use cases and business rules can be tested without user interface, database or other external dependencies.
- Independent of frameworks: the architecture does not depend on invasive frameworks, products, of vendor-specific 
  solutions. Frameworks, tools and libraries are instruments to simplify programming tasks and must be replaceable.
- Independent of the UI: applications do not make assumptions about the UI. The UI can be change easily without
  impacting the rest of the system.
- Independent of the database: the database must be replaceable. It is not allowed to use vendor specific features.
- Independent of any external agency: business rules in the application do not have knowledge of the external world

Consequences:
- Applications do not contain presentation logic.
- Applications may not orchestrate processes across multiple applications. Use choreography or a dedicated
  process orchestrator instead.
- Keep the domain layer framework free from frameworks as much as possible. Restrict dependency injection to 
  the adapter and application layer for wiring the application together at startup.
  
## Microservices architecture

- Applications are decomposed according to business capabilities
- Loose coupling between services, high cohesion inside services
- Developed and maintained by a single team
- Dedicated database per service.
- Private database, data is only accessible via the service's API 
- Pub-sub communication via asynchronous events (event streaming paradigm)
- Independently deployable
- Small and frequent releases

## SOLID principles

Classes are designed according to SOLID principles.

## Grasp principles

Responsibilities are assigned according to GRASP principles.

## Functional style

Prefer immutable objects (values) and functional programming style. A method is either a function without side
effects, or a command that performs some action.

## Testing Pyramid

Tests are written in accordance to the Testing Pyramid.

## Portable builds

It must be possible to build the system with a single command. Builds are portable across environments.

## Binary promotion of artifacts
