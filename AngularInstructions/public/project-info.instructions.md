# project Information
- This file include the project information 
- Do not proceed to analysis or planning without confirming the project information has been reviewed.
- This serves as a **project information bank** for AI assistants like CoPilot or ChatGPT.

# Project Name
Thiqah Mazad - An online auction platform developed for the Saudi Arabian market that enables secure, transparent, and efficient bidding processes for various asset types.

# Project Description
Thiqah Mazad is a comprehensive auction management system that facilitates the entire auction lifecycle from listing to settlement. The platform supports multiple auction types (open, closed, reverse), handles user registration with KYC verification, manages bidding processes, integrates payment gateways, and provides analytics dashboards. The system consists of three main components:

1. **MazadBackEnd**: Core API services and business logic
2. **MazadFrontEnd**: Admin portal for auction management
3. **MazadPublicFrontEnd**: Public-facing application for bidders

# Project Technology
- **Backend Framework**: ASP.NET Core with ASP Zero (ABP) Framework
- **Frontend**: Angular with Metronic UI components
- **Database**: Microsoft SQL Server
- **Authentication**: JWT-based authentication with OAuth 2.0 support
- **DevOps**: Azure DevOps for CI/CD pipelines
- **Cloud Infrastructure**: Microsoft Azure
- **Caching**: Redis
- **Messaging**: only domain events , no meessage gateway
- **Monitoring**: Application Insights
- **Payment Integration**: Multiple payment gateways including SADAD

# Project Architecture
Thiqah Mazad follows a modular monolithic architecture with domain-driven design principles:

- **Core Layer**: Contains domain entities, interfaces, and business logic
  - Domain entities (Auction, Bid, User, etc.)
  - Domain services
  - Repository interfaces
  
- **Application Layer**: Implements use cases and orchestrates domain objects
  - Application services (AuctionAppService, BidAppService, etc.)
  - DTO mappings using AutoMapper
  - Validation using FluentValidation

- **Infrastructure Layer**: Provides technical capabilities and implements interfaces
  - Entity Framework Core data access
  - External service integration
  - Caching implementation
  - Background job processing

- **Web Layer**: Handles HTTP requests and contains API endpoints
  - API controllers
  - Middleware configurations
  - Authentication/Authorization 

- **Frontend Architecture**:
  - Feature-based module organization
  - Reactive state management with NGRX
  - Component-based UI with lazy-loading modules
  - Shared service layer for cross-cutting concerns

# System Architecture Diagram
@startuml
package "Presentation Layer" {
  [API Controllers] 
  [Angular Frontend]
  note bottom of [API Controllers]
    Projects:
    - Thiqah.Mazad.Web.Host
    - Thiqah.Mazad.Web.Core
  end note

  note bottom of [Angular Frontend]
    Project:
    - Angular SPA (separate repo)
  end note
}

package "Application Layer" {
  [Application Services]
  [DTOs]
  [Workflows]
  note bottom of [Application Services]
    Projects:
    - Thiqah.Mazad.Application
    - Thiqah.Mazad.Application.Shared
  end note
}

package "Domain Layer" {
  [Domain Services]
  [Entities]
  [Value Objects]
  [Domain Events]
  [Specifications]
  note bottom of [Domain Services]
    Projects:
    - Thiqah.Mazad.Core
    - Thiqah.Mazad.Core.Shared
  end note
}

package "Infrastructure Layer" {
  [Repositories]
  [Unit of Work]
  [External Services]
  note bottom of [Repositories]
    Projects:
    - Thiqah.Mazad.EntityFrameworkCore
    - Thiqah.Mazad.Integration
  end note
}

[API Controllers] --> [Application Services]
[Angular Frontend] --> [API Controllers]
[Application Services] --> [Domain Services]
[Application Services] --> [Entities]
[Domain Services] --> [Entities]
[Domain Services] --> [Repositories]
[Repositories] --> [Entities]
@enduml