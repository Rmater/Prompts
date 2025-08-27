# Mazad Backend API - Copilot Instructions

## Plan & Act Mode Instructions

You have two modes of operation:

1. **Plan Mode** - Analyze and create strategy without m- **Dropdown (Select List) DTOs**: `[Entity]SelectListItemDto` (e.g., `AuctionSelectListItemDto`) inherit from `Thiqah.Mazad.Common.Dto.BaseSelectListItemDto`

### Generic Implementation Protocol

#### **Pre-Implementation Analysis Requirements**
Before implementing any feature, you MUST:

1. **Entity Structure Discovery**
   ```powershell
   # Examine the target entity class
   read_file /path/to/Entity.cs
   # Search for related entities
   grep_search "class.*Entity" includePattern="**/*.cs"
   # Check existing patterns in the same BusinessContext
   list_dir /path/to/BusinessContext/Dtos
   ```

2. **Data Type Verification Protocol**
   - Read entity definition files to verify EXACT property types
   - Check for nullable types (int?, long?, DateTime?)
   - Verify navigation property names and types
   - Confirm foreign key property names and data types

3. **Pattern Consistency Check**
   - Examine existing DTOs in the same BusinessContext
   - Review existing repository method signatures
   - Check AutoMapper configurations for similar entities
   - Verify test fake repository patterns

#### **Implementation Execution Order**
Execute in this EXACT order to prevent compilation errors:

1. **Phase 1: DTO Creation**
   - Create Input DTO extending `PagedAndSortedInputDto`
   - Create List Item DTO with EXACT entity property types
   - Build to verify DTO compilation

2. **Phase 2: Repository Interface**
   - Update custom repository interface with method signature
   - Use tuple return type: `(int totalCount, List<Entity> entities)`
   - Build to verify interface compilation

3. **Phase 3: Repository Implementation**
   - Implement method in custom repository class
   - Add proper Include() statements for navigation properties
   - Handle nullable properties with safe navigation operators
   - Build to verify repository compilation

4. **Phase 4: Application Service**
   - Add method to application service
   - Use proper authorization attributes
   - Handle DTO mapping with null-safe operations
   - Build to verify service compilation

5. **Phase 5: AutoMapper Configuration**
   - Add entity-to-DTO mapping only (never DTO-to-entity)
   - Use ForMember() for complex mappings
   - Use Ignore() for calculated properties
   - Build to verify mapping compilation

6. **Phase 6: Test Updates**
   - Add method to fake repository with matching signature
   - Return appropriate test data
   - Build test projects to verify compatibility

#### **Error Prevention Strategies**

**Data Type Mismatch Prevention:**
```csharp
// WRONG: Assuming types
public int AssetTypeId { get; set; }  // Could be long!
public DateTime EndDateTime { get; set; }  // Could be nullable!

// RIGHT: Verify from entity
public long AssetTypeId { get; set; }  // Verified from entity
public DateTime? EndDateTime { get; set; }  // Handles nullable
```

**Navigation Property Safety:**
```csharp
// WRONG: Direct property access
AssetTypeName = e.AssetType.Name  // NullReferenceException risk

// RIGHT: Safe navigation
AssetTypeName = e.AssetType?.Name  // Null-safe access
```

**Repository Pattern Compliance:**
```csharp
// WRONG: Direct EF in application service
var query = _context.Auctions.Where(x => x.IsActive);

// RIGHT: Custom repository method
var (totalCount, entities) = await _customRepository.GetListAsync(...);
```

#### **Common Implementation Pitfalls & Solutions**

**Pitfall 1: Property Name Assumptions**
```csharp
// WRONG: Assuming property names
BidsCount = e.Bids.Count()  // Property might be named 'Biddings'

// SOLUTION: Always examine entity first
BidsCount = e.Biddings.Count(b => b.IsActive)  // Use actual property name
```

**Pitfall 2: Collection Property Access**
```csharp
// WRONG: Direct collection access without checking
ParticipantCount = e.AuctionBidders.Count()  // May need filtering

// SOLUTION: Apply business rules
ParticipantCount = e.AuctionBidders.Count(ab => ab.IsActive)
```

**Pitfall 3: Test Repository Neglect**
```csharp
// PROBLEM: Adding method to interface/implementation but forgetting fake
// RESULT: Test compilation fails

// SOLUTION: Always update fake repository
public class FakeEntityCustomRepository : IEntityCustomRepository
{
    public Task<(int totalCount, List<Entity> entities)> GetListAsync(...)
    {
        // Return appropriate test data
        return Task.FromResult((0, new List<Entity>()));
    }
}
```

**Pitfall 4: AutoMapper Bidirectional Mapping**
```csharp
// WRONG: Bidirectional mapping
cfg.CreateMap<Entity, EntityDto>();
cfg.CreateMap<EntityDto, Entity>();  // NEVER do this

// RIGHT: One-way mapping only
cfg.CreateMap<Entity, EntityDto>()
   .ForMember(dest => dest.CalculatedField, opt => opt.Ignore());
```

#### **Implementation Completion Verification**

**Step 1: Code Structure Verification**
```bash
# Verify all files exist in correct locations
# DTOs in BusinessContexts/[Context]/Dtos/
# Repositories in EntityFrameworkCore/BusinessContexts/[Context]/
# Services in Application/BusinessContexts/[Context]/
```

**Step 2: Build Verification Sequence**
```bash
# Build after each phase to catch errors early
dotnet build --no-restore  # After DTOs
dotnet build --no-restore  # After repository interface
dotnet build --no-restore  # After repository implementation
dotnet build --no-restore  # After application service
dotnet build --no-restore  # After AutoMapper
dotnet build --no-restore  # Final verification
```

**Step 3: Implementation Checklist**
- [ ] Input DTO extends PagedAndSortedInputDto
- [ ] List Item DTO has exact entity property types
- [ ] Repository interface updated with tuple return type
- [ ] Repository implementation handles nullable properties safely
- [ ] Application service has authorization attributes
- [ ] AutoMapper uses one-way mapping only
- [ ] Test fake repository updated with matching signatures
- [ ] All builds successful with no compilation errors

**Step 4: Final Quality Check**
- [ ] Property names match entity exactly (not assumed)
- [ ] Data types match entity exactly (long vs int, nullable)
- [ ] Navigation properties use safe access (?.Property)
- [ ] Business logic applied in repository (IsActive filtering)
- [ ] No Entity Framework code in application service
- [ ] AutoMapper ignores calculated/computed properties

### C# Code Quality Standards

- [Backend API Context](#backend-api-context)
  - [System Overview](#system-overview)
  - [Key Business Domains](#key-business-domains)
  - [Core Business Processes](#core-business-processes)
  - [Key Responsibilities](#key-responsibilities)
  - [Technical Architecture](#technical-architecture)

- [Coding Standards and Guidelines](#coding-standards-and-guidelines)
  - [C# Naming Conventions Best Practices](#c-naming-conventions-best-practices)
  - [C# Code Quality Standards](#c-code-quality-standards)
  - [Domain-Driven Design (DDD) Best Practices](#domain-driven-design-ddd-best-practices)
  - [ASP.NET Zero Framework Implementation](#aspnet-zero-framework-implementation)
  - [Database and Entity Management](#database-and-entity-management)
  - [API Design Standards](#api-design-standards)
  - [Background Services and Automation](#background-services-and-automation)
  - [Performance and Scalability](#performance-and-scalability)
  - [Security Best Practices](#security-best-practices)
  - [Testing Standards](#testing-standards)
  - [Monitoring and Observability](#monitoring-and-observability)

## Backend API Context
This is the **core API backend** that powers all Mazad auction platform frontends and provides business logic, data management, and integrations for the **Saudi Legal Auction System**.

### System Overview
The Mazad platform is a comprehensive **legal auction system** for Saudi courts that handles:
- **Asset Auctions**: Real estate, vehicles, and other legal assets
- **Court Integration**: Multi-tenant support for different courts and sections
- **Real-time Bidding**: Live auction participation with auto-bidding capabilities
- **Financial Management**: Wallet-based solvency and payment processing
- **Settlement Processing**: Automated winner determination and bill generation

### Key Business Domains

#### **1. Auction Context (`AuctionContext`)**
- **Core Entities**: `Auction`, `AuctionBidder`, `Bidding`, `AuctionAutoBidding`, `BidderAutoBidding`
- **Business Logic**: Bidding validation, auction timing, settlement processes
- **Real-time Features**: Live bid updates, automatic auction extension, SignalR notifications

#### **2. Asset Context (`AssetContext`)**
- **Core Entities**: `Asset`, `AssetType`, `Cause` (legal cases)
- **Asset Types**: Real estate, vehicles, commercial properties with specific seeking rates
- **Court Structure**: `Court`, `Section` entities for judicial organization

#### **3. Bidder Context (`BidderContext`)**
- **Core Entities**: `Bidder`, linked to `User` for identity management
- **Verification**: Identity validation, phone/email verification via OTP
- **Sales Agents**: External agents representing multiple bidders

#### **4. Wallet Context (`WalletContext`)**
- **Core Entities**: `Wallet`, `WalletHistory`, `ChargeWalletRequest`
- **Financial Tracking**: `TotalAmount`, `FreeAmount`, `ReservedAmount`, `AuctionsReservedAmount`
- **Payment Integration**: Sadad gateway, bill generation, refund processing

#### **5. Payment Context (`PaymentContext`)**
- **Core Entities**: `Bill`, `SettlementBill`, `PaymentLog`
- **Integration**: External payment gateway (Aamaly/Sadad) for Saudi market
- **Audit Trail**: Complete payment tracking and reconciliation

### Core Business Processes

#### **Auction Lifecycle**
```
1. Auction Creation → Asset assignment → Court approval
2. Bidder Registration → Solvency verification → Wallet reservation
3. Live Bidding → Real-time validation → Auto-bidding processing
4. Auction End → Winner determination → Settlement bill generation
5. Payment Processing → Fund release → Asset transfer
```

#### **Bidding Rules**
- **Solvency Requirements**: Wallet funds must cover auction solvency before bidding
- **Minimum Increments**: Each bid must exceed previous by `MinimumBidPrice`
- **Time Extensions**: Bids in final minutes extend auction by configured duration
- **Auto-bidding**: Round-robin system with maximum bid limits per bidder

#### **Financial Calculations**
```csharp
// Core auction pricing formula
decimal seekingAmount = highestBid * SeekingRate / 100;
decimal vatAmount = (highestBid + seekingAmount) * VatRate / 100; // Based on CalculateVatMethod
decimal totalAmount = highestBid + seekingAmount + vatAmount;
```

### Key Responsibilities
- **API Development**: RESTful APIs for web/mobile frontends with specialized endpoints
- **Business Logic**: Complex auction rules, bidding validation, and financial calculations
- **Data Management**: Multi-tenant entity management with soft-delete and audit trails
- **Authentication**: ABP-based auth with role permissions and OTP verification
- **Integrations**: Payment gateways, SMS/email services, file storage (Amazon S3)
- **Background Services**: Auction automation, auto-bidding, settlement processing
- **Real-time Communication**: SignalR hubs for live auction updates

### Technical Architecture
```
📁 Core (Domain Layer)
├── 🏗️ Entities: Rich domain models with business logic
├── 🔧 Domain Services: Cross-entity business operations
├── 📋 Specifications: Reusable query logic
└── 🎯 Events: Domain events for decoupled communication

📁 Application Layer
├── 🚀 Application Services: Use case orchestration
├── 📊 DTOs: API contract definitions
├── 🗺️ AutoMapper: Entity-DTO mapping
└── 🔐 Permissions: ABP authorization

📁 Infrastructure
├── 🗃️ EntityFramework: Data persistence with custom repositories
├── 🌐 External Services: Payment, SMS, email integrations
├── 🔄 Background Jobs: Hangfire for scheduled tasks
└── 📡 SignalR: Real-time communication
```

#### **ASP.NET Zero Framework Patterns**
- **Multi-tenancy**: Support for multiple courts/organizations
- **Application Services**: Coordinate business operations across entities
- **Custom Repositories**: Extended ABP repositories for complex queries
- **Domain Events**: Publish/subscribe for business event handling
- **Background Jobs**: Hangfire integration for auction automation
- **Audit Logging**: Complete audit trails for financial operations

#### **Key Design Patterns**
- **Repository Pattern**: Data access abstraction with Unit of Work
- **Strategy Pattern**: Configurable auction types and bidding strategies
- **Factory Pattern**: Entity creation with business rule validation
- **Observer Pattern**: Domain events and real-time notifications
- **CQRS**: Separate read/write models for complex operations



## Coding Standards and Guidelines

### C# Naming Conventions Best Practices
- **Classes and Interfaces**: PascalCase (e.g., `AuctionService`, `IBidRepository`)
- **Methods and Properties**: PascalCase (e.g., `PlaceBid()`, `CurrentBid`)
- **Fields and Variables**: camelCase (e.g., `minimumBid`, `auctionId`)
- **Private Fields**: Prefix with underscore (e.g., `_auctionRepository`, `_logger`)
- **Constants**: PascalCase (e.g., `MaxBidAmount`, `DefaultTimeoutSeconds`)
- **Enums**: PascalCase for enum and values (e.g., `AuctionStatus.Active`)
- **Entities**: PascalCase for enum and values (e.g., `Auction`)
- **Namespaces**: PascalCase matching folder structure (e.g., `Thiqah.Mazad.Auction.Services`)
- **Event Handlers**: PascalCase matching folder structure (e.g., `AuctionSettledEventHandler`)
- **Application Services**: PascalCase matching folder structure (e.g., `AuctionBiddersAppService`)
- **Domain Services**: PascalCase matching folder structure (e.g., `AuctionAuditLogDomainService`)
- **Interface Files**: Prefix with 'I' (e.g., `IAuctionAppService.cs`)
- **DTOs**: Follow specific naming patterns:
  - Input DTOs: `Get[Entity]ListInput` (e.g., `GetAuctionListInput`)
  - List Result DTOs: `PagedResultDto<[Entity]ListItemDto>` (e.g., `PagedResultDto<AuctionListItemDto>`)
  - Edit DTOs: `[Entity]ForEditDto` (e.g., `AuctionForEditDto`)
  - Create DTOs: `Create[Entity]Dto` (e.g., `CreateAuctionDto`)
  - Update DTOs: `Update[Entity]Dto` (e.g., `UpdateAuctionDto`)
  - Details DTOs: `[Entity]DetailsDto` (e.g., `AuctionDetailsDto`)
  - Dropdown (Select List) DTOs: `[Entity]SelectListItemDto` (e.g., `AuctionSelectListItemDto`) inherit from `Thiqah.Mazad.Common.Dto.BaseSelectListItemDto`


### C# Code Quality Standards

#### **SOLID Principles Implementation**
- **Single Responsibility**: Each class should have one reason to change
- **Open/Closed**: Open for extension, closed for modification (use interfaces and inheritance)
- **Liskov Substitution**: Derived classes must be substitutable for base classes
- **Interface Segregation**: Create specific interfaces rather than monolithic ones
- **Dependency Inversion**: Depend on abstractions, not concretions (use dependency injection)

#### **Exception Handling Best Practices**
- **Custom Exceptions**: Create domain-specific exceptions inheriting from `UserFriendlyException`
- **Exception Messages**: Use localized, user-friendly messages for business exceptions
- **Logging**: Always log exceptions with sufficient context for debugging
- **Exception Wrapping**: Wrap infrastructure exceptions in domain exceptions
- **Finally Blocks**: Use `using` statements or try-finally for resource cleanup

#### **Async/Await Best Practices**
- **Async All the Way**: Use async/await throughout the call chain
- **Async Naming**: Suffix async methods with 'Async' (e.g., `PlaceBidAsync`)
- **Avoid Blocking**: Never use `.Result` or `.Wait()` on async operations
- **Cancellation Tokens**: Accept and propagate CancellationTokens for long-running operations

#### **Performance Best Practices**
- **Database Queries**: Use projection to select only needed columns
- **Lazy Loading**: Avoid N+1 queries by using Include() or Load() appropriately
- **Memory Management**: Dispose of resources properly using `using` statements
- **String Concatenation**: Use StringBuilder for multiple concatenations
- **Collections**: Use appropriate collection types (List vs HashSet vs Dictionary)

#### **AutoMapper Usage Guidelines - CRITICAL**
- **NEVER Map DTO to Entity**: Use entity constructors or domain methods instead of `ObjectMapper.Map<Entity>(dto)`
- **One-Way Mapping Only**: AutoMapper should only map FROM entities TO DTOs, never the reverse
- **Domain Integrity**: Always preserve business rules and aggregate boundaries during entity creation

### Domain-Driven Design (DDD) Best Practices

#### **Bounded Context Guidelines**
- **Context Boundaries**: Clearly define boundaries between auction, bidder, payment, and asset contexts
- **Ubiquitous Language**: Use consistent terminology within each context (e.g., "Solvency" not "Deposit")
- **Context Integration**: Use domain events for communication between contexts
- **Anti-Corruption Layer**: Protect domain model from external service complexities

#### **Aggregate Design Patterns**
- **Aggregate Roots**: Only `Auction`, `Bidder`, `Wallet` should be aggregate roots
- **Aggregate Boundaries**: Keep aggregates small and focused on transaction boundaries
- **Reference by ID**: Reference other aggregates by ID, not direct object references
- **Invariant Protection**: Ensure business rules are enforced within aggregate boundaries

```csharp
// Good aggregate example
public class Auction : FullAuditedAggregateRoot<long>
{
    public virtual void PlaceBid(decimal bidAmount, long bidderId)
    {
        // Validate business rules within aggregate
        if (Status != AuctionStatus.Active)
            throw new BusinessException("CannotBidOnInactiveAuction");
            
        if (bidAmount <= HighestBid)
            throw new BusinessException("BidMustBeHigherThanCurrent");
            
        if (bidAmount < (HighestBid + MinimumBidPrice))
            throw new BusinessException("BidIncrementTooSmall");
            
        // Update state
        HighestBid = bidAmount;
        WinningBidderId = bidderId;
        
        // Publish domain event
        AddDomainEvent(new BidPlacedEvent(Id, bidAmount, bidderId));
    }
}
```

#### **Domain Service vs Application Service Usage**
- **Domain Services**: Use for business logic that doesn't belong to a single entity
- **Application Services**: Coordinate between domain objects and handle DTOs
- **Cross-Aggregate Operations**: Domain services for operations spanning multiple aggregates
- **External Integrations**: Application services for calling external APIs

```csharp
// Domain Service example
public class AuctionDomainService : DomainService
{
    public async Task<bool> CanBidderParticipateAsync(Auction auction, Bidder bidder)
    {
        // Complex business logic involving multiple entities
        var hasRequiredSolvency = await CheckSolvencyRequirementsAsync(auction, bidder);
        var isEligibleBidder = CheckBidderEligibility(auction, bidder);
        var isWithinTimeLimit = CheckAuctionTimeLimits(auction);
        
        return hasRequiredSolvency && isEligibleBidder && isWithinTimeLimit;
    }
}
```

#### **Value Object Implementation**
- **Immutability**: Value objects should be immutable after creation
- **Equality**: Override Equals and GetHashCode based on value semantics
- **Validation**: Validate invariants in constructor
- **Primitive Obsession**: Replace primitive types with meaningful value objects

```csharp
// Good value object example
public class Money : ValueObject
{
    public decimal Amount { get; private set; }
    public string Currency { get; private set; }
    
    public Money(decimal amount, string currency)
    {
        if (amount < 0)
            throw new ArgumentException("Amount cannot be negative");
        if (string.IsNullOrEmpty(currency))
            throw new ArgumentException("Currency is required");
            
        Amount = amount;
        Currency = currency;
    }
    
    protected override IEnumerable<object> GetEqualityComponents()
    {
        yield return Amount;
        yield return Currency;
    }
    
    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new InvalidOperationException("Cannot add money with different currencies");
        return new Money(Amount + other.Amount, Currency);
    }
}
```

#### **Domain Event Patterns**
- **Event Publishing**: Publish events from aggregate roots when important business events occur
- **Event Handling**: Use event handlers for side effects and cross-context communication
- **Event Ordering**: Consider event ordering for related business operations
- **Event Store**: Consider event sourcing for audit-heavy domains like financial transactions

```csharp
// Domain event example
public class BidPlacedEvent : DomainEventData
{
    public long AuctionId { get; set; }
    public decimal BidAmount { get; set; }
    public long BidderId { get; set; }
    public DateTime BidTime { get; set; }
    
    public BidPlacedEvent(long auctionId, decimal bidAmount, long bidderId)
    {
        AuctionId = auctionId;
        BidAmount = bidAmount;
        BidderId = bidderId;
        BidTime = Clock.Now;
    }
}

// Event handler example
public class BidPlacedEventHandler : IEventHandler<BidPlacedEvent>
{
    public async Task HandleEventAsync(BidPlacedEvent eventData)
    {
        // Send real-time notification
        await _signalRService.NotifyBidPlaced(eventData);
        
        // Update wallet reservations
        await _walletService.UpdateReservationsAfterBid(eventData);
        
        // Trigger auto-bidding if configured
        await _autoBiddingService.ProcessAutoBidsAsync(eventData.AuctionId);
    }
}
```

### ASP.NET Zero Framework Implementation

#### **Application Layer Best Practices**
- **Application Services**: Use for coordinating business operations across multiple domain entities
- **DTOs (Data Transfer Objects)**: Create specific DTOs for each operation to ensure API contract stability
- **AutoMapper Profiles**: Define mapping configurations between entities and DTOs
- **Permission Checking**: Use ABP's permission system for authorization at application service level

#### **Domain Layer Implementation**
- **Entities**: Follow ABP entity conventions with proper audit properties
- **Domain Services**: Implement business logic that doesn't belong to a single entity
- **Specifications**: Use specification pattern for complex query logic
- **Domain Events**: Publish events for important business operations

#### **Repository Pattern Guidelines**
- **Custom Repositories**: Extend ABP's base repository for complex queries - **MANDATORY for all GetListAsync operations**
- **List Method Signature**: Custom repositories must return `(int totalCount, List<Entity> entities)` for list operations
- **Specifications**: Use for building complex, reusable query logic
- **Unit of Work**: Leverage ABP's automatic UoW for transaction management
- **Soft Delete**: Use ABP's soft delete for maintaining data integrity
- **EF Core Isolation**: ALL Entity Framework code must stay in repositories, never in application services

### Database and Entity Management

#### **Entity Framework Core Standards**
- **Migrations**: Use code-first migrations with proper naming conventions
- **Fluent API**: Configure complex relationships and constraints in DbContext
- **Indexes**: Create appropriate indexes for query performance
- **Audit Properties**: Leverage ABP's automatic audit property population

#### **Performance Optimization**
- **Lazy Loading**: Use judiciously to avoid N+1 query problems
- **Eager Loading**: Include related entities when needed upfront
- **Query Optimization**: Use projection with Select to load only needed data
- **Bulk Operations**: Use bulk insert/update for large datasets

### API Design Standards

#### **Application Service Method Naming Conventions**
- **GetAsync**: Retrieve a single entity by ID
- **GetListAsync**: Retrieve a paginated list of entities - **MUST follow mandatory pattern below**
- **GetSelectListAsync**: Retrieve a list of entities for dropdown/select options
- **CreateAsync**: Create a new entity
- **GetForEditAsync**: Retrieve entity data for editing purposes
- **UpdateAsync**: Update an existing entity
- **DeleteAsync**: Delete an entity (soft delete)
- **ApproveAsync**: Approve an entity (business-specific action)
- **RejectAsync**: Reject an entity (business-specific action)


#### **MANDATORY GetListAsync Implementation Pattern**
```csharp
public async Task<PagedResultDto<EntityListItemDto>> GetListAsync(GetEntityListInput input)
{
    var (totalCount, entities) = await _entityCustomRepository.GetEntitiesListAsync(
        // Pass all input parameters explicitly
        simpleSearch: input.SimpleSearch,
        sorting: input.Sorting,
        skipCount: input.SkipCount,
        maxResultCount: input.MaxResultCount
        // Add other specific filters as needed
    );

    var entityListItems = entities.Select(e => new EntityListItemDto
    {
        // Map entity properties to DTO
    }).ToList();

    return new PagedResultDto<EntityListItemDto>(totalCount, entityListItems);
}
```

**CRITICAL Implementation Rules:**
- ALL EF Core code must be in custom repository methods
- Custom repository returns `(int totalCount, List<Entity> entities)` tuple
- Application service handles DTO projection only
- No direct DbContext usage in application services

#### **MANDATORY Implementation Checklist - FOLLOW STRICTLY**

**Before Starting Implementation:**
1. ✅ **Examine Entity Properties**: Use `read_file` or `grep_search` to examine the actual entity class for EXACT property names and data types
2. ✅ **Check Existing Patterns**: Look at existing DTOs in the same BusinessContext for naming and property type consistency
3. ✅ **Verify Relationships**: Check navigation properties and their actual property names in entity classes
4. ✅ **Review Repository Patterns**: Examine existing custom repository methods for signature patterns

**During Implementation:**
5. ✅ **Data Type Alignment**: Ensure DTO property types EXACTLY match entity property types (long vs int, nullable vs non-nullable)
6. ✅ **Property Name Accuracy**: Use EXACT property names from entities, not assumed names
7. ✅ **Nullable Handling**: Handle nullable properties with proper null checks (?.HasValue, ??)
8. ✅ **Navigation Properties**: Use correct navigation property paths (e.g., entity.RelatedEntity?.PropertyName)
9. ✅ **Repository Signatures**: Follow the EXACT pattern: `Task<(int totalCount, List<Entity> entities)> GetEntitiesListAsync(...)`

**After Implementation:**
10. ✅ **Build Verification**: Run `dotnet build` after each major component to catch errors early
11. ✅ **Test Repository Update**: Update corresponding fake repositories in test projects with matching method signatures
12. ✅ **AutoMapper Validation**: Ensure mapping configurations use `.ForMember()` for complex mappings and `.Ignore()` for calculated fields

**Common Error Prevention:**
- **Data Types**: NEVER assume int/long types - always verify from entity definition
- **Nullable Properties**: Handle with `entity.Property?.ToString()` or `entity.Property ?? defaultValue`
- **Entity Navigation**: Use `Include()` statements in repository for related entities needed in DTOs
- **Property Access**: Check if property exists before using (some entities may not have expected properties)
- **Test Compatibility**: Always add missing methods to fake repositories to prevent test compilation errors

#### **DTO Naming Conventions**
- **Input DTOs**: `Get[Entity]ListInput` for list filtering and pagination
- **List Result DTOs**: `PagedResultDto<[Entity]ListItemDto>` for paginated results
- **Edit DTOs**: `[Entity]ForEditDto` for edit form data
- **Create DTOs**: `Create[Entity]Dto` for entity creation
- **Update DTOs**: `Update[Entity]Dto` for entity updates
- **Details DTOs**: `[Entity]DetailsDto` for detailed entity information

- **Select List DTOs**: `[Entity]SelectListItemDto` for dropdown/select list options

```csharp
// Example Application Service Interface
public interface IAuctionAppService : IApplicationService
{
    Task<AuctionDetailsDto> GetAsync(long id);
    Task<PagedResultDto<AuctionListItemDto>> GetListAsync(GetAuctionListInput input);
    Task<AuctionSelectListItemDto> GetSelectListAsync();
    Task<AuctionDetailsDto> CreateAsync(CreateAuctionDto input);
    Task<AuctionForEditDto> GetForEditAsync(long id);
    Task<AuctionDetailsDto> UpdateAsync(UpdateAuctionDto input);
    Task DeleteAsync(long id);
    Task<AuctionDetailsDto> ApproveAsync(long id);
    Task<AuctionDetailsDto> RejectAsync(long id);
}
```

#### **RESTful API Guidelines**
- **HTTP Verbs**: Use appropriate verbs (GET, POST, PUT, DELETE) for operations
- **Status Codes**: Return meaningful HTTP status codes with consistent error responses
- **Pagination**: Implement consistent pagination for list endpoints with proper metadata
- **Filtering**: Support query parameters for filtering, sorting, and searching

#### **Error Handling Standards**
- **Exception Handling**: Use ABP's exception handling with custom business exceptions
- **Validation**: Implement both client and server-side validation with detailed error messages
- **Error Responses**: Consistent error response format across all endpoints
- **Logging**: Comprehensive logging for debugging, monitoring, and audit trails

#### **Authentication and Authorization**
- **JWT Tokens**: Implement secure token-based authentication with refresh tokens
- **Permission System**: Use ABP's fine-grained permission system for feature access
- **Role Management**: Implement hierarchical roles (Court Admin, Bidder, Sales Agent, etc.)
- **OTP Verification**: Multi-factor authentication for sensitive operations

### Background Services and Automation

#### **Hangfire Integration Standards**
- **Recurring Jobs**: Schedule regular tasks (auction notifications, settlement processing)
- **Fire-and-Forget**: Queue immediate tasks (email sending, SMS notifications)
- **Delayed Jobs**: Schedule future tasks (auction start/end automation, payment reminders)
- **Job Monitoring**: Implement proper error handling, retry logic, and job status tracking

#### **Event-Driven Architecture**
- **Domain Events**: Publish events for important business operations (bid placed, auction ended)
- **Event Handlers**: Implement handlers for cross-cutting concerns (notifications, audit logging)
- **Integration Events**: Handle communication between bounded contexts
- **SignalR Events**: Real-time notifications for live auction updates

### Performance and Scalability

#### **Caching Strategies**
- **Memory Caching**: Cache frequently accessed data (auction details, user permissions, court settings)
- **Distributed Caching**: Use Redis for multi-instance deployments and session management
- **Cache Invalidation**: Implement proper cache invalidation for auction and bidder data
- **HTTP Caching**: Set appropriate cache headers for static auction images and documents

#### **Database Optimization**
- **Connection Pooling**: Configure appropriate connection pool sizes for high concurrency
- **Query Optimization**: Use Entity Framework query analysis tools and SQL profiling
- **Bulk Operations**: Implement efficient bulk operations for bid processing and reporting
- **Read Replicas**: Consider read replicas for reporting and auction viewing queries

### Security Best Practices

#### **Data Protection Standards**
- **Input Validation**: Validate all inputs at API boundaries with specific validation rules
- **SQL Injection**: Use parameterized queries and avoid dynamic SQL construction
- **XSS Prevention**: Sanitize output and use proper encoding for user-generated content
- **Sensitive Data**: Encrypt sensitive financial data and personal information

#### **API Security Implementation**
- **CORS Configuration**: Proper CORS setup for web and mobile frontend applications
- **Rate Limiting**: Implement rate limiting to prevent abuse during high-activity auctions
- **Request Validation**: Validate request size, content type, and format for file uploads
- **Security Headers**: Set appropriate security headers in all API responses

### Testing Standards

#### **Unit Testing Guidelines**
- **Domain Logic**: Test business rules and domain services thoroughly with comprehensive scenarios
- **Application Services**: Test application service logic with mocked dependencies
- **Repository Tests**: Test custom repository methods with in-memory database
- **Test Data Builders**: Use builder pattern for creating complex test auction and bidder data

#### **Integration Testing Standards**
- **API Testing**: Test complete request/response cycles for critical auction operations
- **Database Testing**: Test with actual database for complex auction and bidding queries
- **SignalR Testing**: Test real-time functionality with test clients for bid updates
- **Background Job Testing**: Test auction automation and settlement job execution

### Monitoring and Observability

#### **Logging Standards**
- **Structured Logging**: Use structured logging with Serilog for searchable logs
- **Log Levels**: Use appropriate log levels (Debug, Info, Warning, Error, Fatal)
- **Correlation IDs**: Track requests across service boundaries for auction operations
- **Performance Logging**: Log slow queries, bid processing times, and payment operations

#### **Health Monitoring**
- **Database Health**: Monitor database connectivity and query performance
- **External Services**: Check health of payment gateways and SMS/email services
- **Background Jobs**: Monitor auction automation job queue health and processing times
- **Resource Monitoring**: Track memory, CPU, and connection pool utilization
