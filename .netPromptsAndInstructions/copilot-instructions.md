# Mazad Backend API - Copilot Instructions

@include ../../../.github/shared/base-copilot-instructions.md

## Backend API Context
This is the **core API backend** that powers all Mazad auction platform frontends and provides business logic, data management, and integrations.

### Key Responsibilities
- **API Development**: RESTful APIs for all frontend applications
- **Business Logic**: Core auction rules, bidding logic, and validation
- **Data Management**: Entity management, database operations, and migrations
- **Authentication**: User authentication, authorization, and role management
- **Integrations**: Third-party services (payments, notifications, external APIs)
- **Background Services**: Scheduled tasks, auction automation, and notifications

### Technical Focus Areas
- **ASP.NET Zero Framework**: Follow ABP framework patterns and conventions
- **Clean Architecture**: Maintain separation of concerns across layers
- **Domain-Driven Design**: Implement rich domain models with business rules
- **CQRS Pattern**: Command Query Responsibility Segregation for complex operations
- **Event Sourcing**: Audit trails and domain events for business operations
- **Multi-tenancy**: Support multiple auction houses on single infrastructure

### Backend Specific Patterns
- **Repository Pattern**: Data access abstraction with Unit of Work
- **Application Services**: Coordinate between domain and infrastructure layers
- **Domain Events**: Decouple business logic through event-driven architecture
- **Background Jobs**: Hangfire integration for scheduled and recurring tasks
- **SignalR Hubs**: Real-time communication for live auction updates
- **Custom Exceptions**: Business-specific exception handling and error responses

## Coding Standards and Guidelines
@include .github/backend-api-guidelines.instructions.md
