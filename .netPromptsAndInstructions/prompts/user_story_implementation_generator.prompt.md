# Professional User Story Implementation Generator for ASP.NET Zero

## Executive Summary

This prompt generates **complete, production-ready implementations** for user stories in the **Mazad Backend API** system. It supports all CRUD operations (Create, Read, Update, Delete) and custom business operations while maintaining strict compliance with established project standards, coding conventions, and architectural patterns.

**Scope**: Complete user story implementation including DTOs, Application Services, Custom Repositories, AutoMapper Profiles, and Permission Management.

**Quality Standards**: Enterprise-grade, maintainable, and performance-optimized code following Domain-Driven Design (DDD) principles and ASP.NET Zero framework conventions.

## Project Context & Architecture

### System Overview
The **Mazad Backend API** is a comprehensive legal auction system for Saudi courts built with:
- **Framework**: ASP.NET Zero with ABP Framework
- **Architecture**: Domain-Driven Design (DDD) with Clean Architecture principles
- **Database**: Entity Framework Core with SQL Server
- **Authentication**: JWT with ABP's multi-tenant authorization system
- **Real-time**: SignalR for live auction updates
- **Background Processing**: Hangfire for auction automation

### Bounded Contexts
```
📁 Thiqah.Mazad.Core
├── 🏛️ Auction Context: Auctions, Bidding, Settlement
├── 👤 Bidder Context: Bidders, Identity, Verification  
├── 💰 Wallet Context: Financial management, Reservations
├── 💳 Payment Context: Bills, Transactions, Gateway integration
├── 🏢 Asset Context: Assets, Courts, Legal cases
└── 🔐 Authorization Context: Users, Roles, Permissions
```

## ⚠️ CRITICAL: Mandatory Compliance Requirements

**ABSOLUTE REQUIREMENTS**: Every generated component MUST follow these non-negotiable standards from the project instructions:

### 1. **Repository Pattern (MANDATORY)**
```csharp
// REQUIRED SIGNATURE for all GetListAsync methods
Task<(int totalCount, List<Entity> entities)> GetEntitiesListAsync(
    string simpleSearch,
    string sorting,
    int skipCount,
    int maxResultCount
    // Additional specific filters
);
```

### 2. **Application Service Pattern (MANDATORY)**
```csharp
public async Task<PagedResultDto<EntityListItemDto>> GetListAsync(GetEntityListInput input)
{
    // NO EF Core code here - repository calls only
    var (totalCount, entities) = await _entityCustomRepository.GetEntitiesListAsync(/*params*/);
    
    // DTO projection only
    var entityListItems = entities.Select(e => new EntityListItemDto { /*mapping*/ }).ToList();
    
    return new PagedResultDto<EntityListItemDto>(totalCount, entityListItems);
}
```

### 3. **AutoMapper Rules (MANDATORY)**
- **NEVER**: Map DTO to Entity (`ObjectMapper.Map<Entity>(dto)`)
- **ALWAYS**: Map Entity to DTO only
- **Entity Creation**: Use constructors or domain methods only

### 4. **Naming Conventions (MANDATORY)**
- **Input DTOs**: `Get[Entity]ListInput`, `Create[Entity]Dto`, `Update[Entity]Dto`
- **Output DTOs**: `[Entity]ListItemDto`, `[Entity]DetailsDto`, `[Entity]ForEditDto`
- **Select DTOs**: `[Entity]SelectListItemDto` inherits from `BaseSelectListItemDto`
- **Services**: `[Entity]AppService` implements `I[Entity]AppService`

## User Story Analysis Framework

### Step 1: Extract Core Information
Analyze the provided user story JSON and extract:

```json
{
  "entity_analysis": {
    "primary_entity": "[DETECT from user_story context and UI models]",
    "bounded_context": "[Auction|Bidder|Wallet|Payment|Asset|Authorization]", 
    "namespace_base": "Thiqah.Mazad.[Context]"
  },
  "operation_analysis": {
    "operation_types": "[List|Create|Update|GetById|Delete|Custom]",
    "complexity_level": "[Simple|Complex|Enterprise]",
    "business_rules_count": "[COUNT BR-X rules]"
  },
  "ui_analysis": {
    "ui_models": "[EXTRACT all نموذج-XXX]",
    "filter_complexity": "[Simple|Advanced|Complex]",
    "validation_requirements": "[EXTRACT from filters and business rules]"
  }
}
```

### Step 2: Operation Type Detection
Determine implementation patterns based on user story content:

#### **List Operations** (GetListAsync)
- **Indicators**: "استعراض قائمة", "البحث", "التصفية", UI models with columns
- **Components**: Input DTOs, List DTOs, Custom Repository with filtering
- **Business Logic**: Sorting, pagination, complex filtering

#### **Create Operations** (CreateAsync)
- **Indicators**: "إنشاء", "إضافة", "تسجيل", form models
- **Components**: Create DTOs, validation, domain entity constructors
- **Business Logic**: Validation rules, business invariants, domain events

#### **Update Operations** (UpdateAsync)
- **Indicators**: "تعديل", "تحديث", "تحرير", edit forms
- **Components**: Update DTOs, GetForEdit DTOs, optimistic concurrency
- **Business Logic**: Partial updates, state transitions, authorization

#### **Detail Operations** (GetAsync)
- **Indicators**: "تفاصيل", "عرض", detail views
- **Components**: Details DTOs, complex projections
- **Business Logic**: Authorization, related data loading

#### **Custom Business Operations**
- **Indicators**: "اعتماد", "رفض", "تفعيل", custom actions
- **Components**: Specific DTOs, domain service calls
- **Business Logic**: State machines, business workflows

### Step 3: Complexity Assessment

#### **Simple Operations**
- Basic CRUD with minimal business rules
- Standard UI patterns
- Single entity operations

#### **Complex Operations**
- Multiple business rules (3+ BR-X)
- Advanced filtering and sorting
- Cross-context operations

#### **Enterprise Operations**
- Complex business workflows
- Integration with external systems
- Advanced security requirements

## Implementation Architecture by Operation Type

### 1. List Operations Implementation

#### DTOs Structure
```csharp
namespace Thiqah.Mazad.[Context].Dtos
{
    // Input DTO for filtering and pagination
    public class Get[Entity]ListInput : PagedAndSortedInputDto
    {
        [MaxLength(50)]
        public string SimpleSearch { get; set; }
        
        public List<int> StatusIds { get; set; }
        public List<int> TypeIds { get; set; }
        public DateTime? FromDate { get; set; }
        public DateTime? ToDate { get; set; }
        // Additional filters from user story
    }
    
    // Output DTO for list display
    public class [Entity]ListItemDto
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public int StatusId { get; set; }
        public string StatusName { get; set; }
        public int TypeId { get; set; }
        public string TypeName { get; set; }
        public DateTime CreationTime { get; set; }
        // Additional columns from UI models
    }
    
    // Select list DTO for dropdowns
    public class [Entity]SelectListItemDto : BaseSelectListItemDto
    {
        public string Code { get; set; }
        public int StatusId { get; set; }
    }
}
```

#### Custom Repository Pattern
```csharp
public interface I[Entity]CustomRepository : IRepository<[Entity], long>
{
    Task<(int totalCount, List<[Entity]> entities)> Get[Entity]ListAsync(
        string simpleSearch,
        List<int> statusIds,
        List<int> typeIds,
        DateTime? fromDate,
        DateTime? toDate,
        string sorting,
        int skipCount,
        int maxResultCount
    );
}

public class [Entity]CustomRepository : EfCoreRepositoryBase<MazadDbContext, [Entity], long>, I[Entity]CustomRepository
{
    public async Task<(int totalCount, List<[Entity]> entities)> Get[Entity]ListAsync(
        string simpleSearch,
        List<int> statusIds,
        List<int> typeIds,
        DateTime? fromDate,
        DateTime? toDate,
        string sorting,
        int skipCount,
        int maxResultCount)
    {
        var query = GetAll()
            .Include(e => e.Status)
            .Include(e => e.Type)
            .WhereIf(!string.IsNullOrWhiteSpace(simpleSearch), 
                e => e.Name.Contains(simpleSearch) || e.Code.Contains(simpleSearch))
            .WhereIf(statusIds?.Any() == true, e => statusIds.Contains(e.StatusId))
            .WhereIf(typeIds?.Any() == true, e => typeIds.Contains(e.TypeId))
            .WhereIf(fromDate.HasValue, e => e.CreationTime >= fromDate.Value)
            .WhereIf(toDate.HasValue, e => e.CreationTime <= toDate.Value);

        // Apply business rule sorting if specified
        query = ApplyBusinessRuleSorting(query, sorting);

        var totalCount = await query.CountAsync();
        var entities = await query
            .OrderBy(sorting.IsNullOrWhiteSpace() ? "CreationTime DESC" : sorting)
            .PageBy(skipCount, maxResultCount)
            .ToListAsync();

        return (totalCount, entities);
    }
}
```

### 2. Create Operations Implementation

#### DTOs Structure
```csharp
public class Create[Entity]Dto
{
    [Required]
    [MaxLength(100)]
    public string Name { get; set; }
    
    [Required]
    public int TypeId { get; set; }
    
    public int? StatusId { get; set; }
    
    [MaxLength(500)]
    public string Description { get; set; }
    
    // Additional properties from user story forms
}

public class [Entity]DetailsDto
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int TypeId { get; set; }
    public string TypeName { get; set; }
    public int StatusId { get; set; }
    public string StatusName { get; set; }
    public string Description { get; set; }
    public DateTime CreationTime { get; set; }
    public string CreatorUserName { get; set; }
    // Additional detail properties
}
```

#### Application Service Pattern
```csharp
public async Task<[Entity]DetailsDto> CreateAsync(Create[Entity]Dto input)
{
    // Validation
    await ValidateCreate[Entity]Input(input);
    
    // Create entity using domain constructor (NEVER use AutoMapper)
    var entity = new [Entity](
        input.Name,
        input.TypeId,
        input.Description
    );
    
    // Apply business rules
    await Apply[Entity]BusinessRules(entity);
    
    // Save to repository
    await _[entity]Repository.InsertAsync(entity);
    await CurrentUnitOfWork.SaveChangesAsync();
    
    // Return details DTO
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

### 3. Update Operations Implementation

#### DTOs Structure
```csharp
public class Update[Entity]Dto
{
    [Required]
    public long Id { get; set; }
    
    [Required]
    [MaxLength(100)]
    public string Name { get; set; }
    
    public int TypeId { get; set; }
    
    [MaxLength(500)]
    public string Description { get; set; }
    
    // Concurrency handling
    public byte[] RowVersion { get; set; }
}

public class [Entity]ForEditDto
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int TypeId { get; set; }
    public string Description { get; set; }
    public byte[] RowVersion { get; set; }
    // Additional edit properties
}
```

#### Application Service Pattern
```csharp
public async Task<[Entity]ForEditDto> GetForEditAsync(long id)
{
    var entity = await _[entity]Repository.GetAsync(id);
    return ObjectMapper.Map<[Entity]ForEditDto>(entity);
}

public async Task<[Entity]DetailsDto> UpdateAsync(Update[Entity]Dto input)
{
    // Get and validate entity
    var entity = await _[entity]Repository.GetAsync(input.Id);
    
    // Update using domain methods (NEVER use AutoMapper)
    entity.Update(input.Name, input.TypeId, input.Description);
    
    // Apply business rules
    await Apply[Entity]UpdateBusinessRules(entity);
    
    // Save changes
    await _[entity]Repository.UpdateAsync(entity);
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

### 4. Detail Operations Implementation

```csharp
public async Task<[Entity]DetailsDto> GetAsync(long id)
{
    var entity = await _[entity]Repository.GetAllIncluding(
        e => e.Status,
        e => e.Type,
        e => e.CreatorUser
    ).FirstOrDefaultAsync(e => e.Id == id);
    
    if (entity == null)
    {
        throw new EntityNotFoundException(typeof([Entity]), id);
    }
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

### 5. Custom Business Operations Implementation

```csharp
public async Task<[Entity]DetailsDto> ApproveAsync(long id)
{
    var entity = await _[entity]Repository.GetAsync(id);
    
    // Use domain method for state transition
    entity.Approve();
    
    // Publish domain event
    await EventBus.TriggerAsync(new [Entity]ApprovedEvent(entity.Id));
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}

public async Task<[Entity]DetailsDto> RejectAsync(long id, string reason)
{
    var entity = await _[entity]Repository.GetAsync(id);
    
    // Use domain method with business logic
    entity.Reject(reason);
    
    // Publish domain event
    await EventBus.TriggerAsync(new [Entity]RejectedEvent(entity.Id, reason));
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

## Domain Context

### Enum References
[EXTRACT FROM JSON: user_story.domain_enums]
```csharp
[FOR EACH enum in domain_enums]:
// [EnumName] values:
[FOR EACH enum value]: // [key] = [name] ([display_name])
```

## Advanced Technical Implementation Guidelines

### Performance Optimization Strategies

#### Database Query Optimization
```csharp
// Efficient query patterns
public async Task<(int totalCount, List<Entity> entities)> GetOptimizedListAsync()
{
    // Use projection for large datasets
    var query = GetAll()
        .Select(e => new EntityListProjection
        {
            Id = e.Id,
            Name = e.Name,
            StatusId = e.StatusId,
            StatusName = e.Status.Name,
            CreationTime = e.CreationTime
        });
    
    // Count with optimized query
    var totalCount = await query.CountAsync();
    
    // Paginated results
    var entities = await query
        .OrderByDescending(e => e.CreationTime)
        .Skip(skipCount)
        .Take(maxResultCount)
        .ToListAsync();
    
    return (totalCount, entities.Select(p => MapToEntity(p)).ToList());
}
```

#### Memory Optimization
```csharp
// Streaming for large datasets
public async IAsyncEnumerable<EntityListItemDto> GetLargeDatasetAsync()
{
    var query = GetAll().AsAsyncEnumerable();
    
    await foreach (var entity in query)
    {
        yield return ObjectMapper.Map<EntityListItemDto>(entity);
    }
}
```

### Security Implementation Patterns

#### Authorization Strategies
```csharp
[AbpAuthorize(PermissionNames.Pages_[Entity]_View)]
public async Task<PagedResultDto<[Entity]ListItemDto>> GetListAsync(Get[Entity]ListInput input)
{
    // Data filtering based on user permissions
    var filteredQuery = await ApplyUserDataFilterAsync(input);
    
    // Multi-tenant data isolation
    var tenantFilteredQuery = ApplyTenantFilter(filteredQuery);
    
    // Execute with security context
    return await ExecuteSecureQuery(tenantFilteredQuery);
}

private async Task<IQueryable<[Entity]>> ApplyUserDataFilterAsync(Get[Entity]ListInput input)
{
    var currentUser = await GetCurrentUserAsync();
    
    // Apply role-based filtering
    if (currentUser.IsInRole("SalesAgent"))
    {
        return GetAll().Where(e => e.CreatorUserId == currentUser.Id);
    }
    
    // Court-specific filtering
    if (currentUser.IsInRole("CourtAdmin"))
    {
        return GetAll().Where(e => e.CourtId == currentUser.CourtId);
    }
    
    return GetAll();
}
```

#### Input Validation and Sanitization
```csharp
private async Task ValidateCreate[Entity]Input(Create[Entity]Dto input)
{
    // Business rule validation
    if (await _[entity]Repository.CountAsync(e => e.Name == input.Name) > 0)
    {
        throw new UserFriendlyException(L("[Entity]NameAlreadyExists"));
    }
    
    // Cross-entity validation
    if (input.TypeId > 0 && !await _typeRepository.AnyAsync(t => t.Id == input.TypeId))
    {
        throw new UserFriendlyException(L("Invalid[Entity]Type"));
    }
    
    // Business logic validation
    await _[entity]DomainService.ValidateBusinessRulesAsync(input);
}
```

### Business Rules Implementation

#### Complex Sorting Implementation (BR-2 Pattern)
```csharp
private IQueryable<[Entity]> ApplyBusinessRuleSorting(IQueryable<[Entity]> query, string sorting)
{
    if (string.IsNullOrWhiteSpace(sorting))
    {
        // Default business rule sorting
        return query
            .OrderBy(e => e.Status == EntityStatus.InProgress ? 1 :
                         e.Status == EntityStatus.Upcoming ? 2 :
                         e.Status == EntityStatus.Completed ? 3 : 4)
            .ThenBy(e => e.Status == EntityStatus.InProgress ? e.EndTime :
                        e.Status == EntityStatus.Upcoming ? e.StartTime :
                        e.CreationTime);
    }
    
    return query.OrderBy(sorting);
}
```

#### State Machine Implementation
```csharp
public class [Entity] : FullAuditedAggregateRoot<long>
{
    public virtual void Approve()
    {
        if (Status != EntityStatus.WaitingApproval)
        {
            throw new BusinessException("[Entity]CannotBeApproved");
        }
        
        Status = EntityStatus.Approved;
        ApprovalTime = Clock.Now;
        
        // Publish domain event
        AddDomainEvent(new [Entity]ApprovedEvent(this));
    }
    
    public virtual void Reject(string reason)
    {
        if (Status != EntityStatus.WaitingApproval)
        {
            throw new BusinessException("[Entity]CannotBeRejected");
        }
        
        if (string.IsNullOrWhiteSpace(reason))
        {
            throw new BusinessException("RejectionReasonRequired");
        }
        
        Status = EntityStatus.Rejected;
        RejectionReason = reason;
        RejectionTime = Clock.Now;
        
        // Publish domain event
        AddDomainEvent(new [Entity]RejectedEvent(this, reason));
    }
}
```

### Error Handling and Logging

#### Exception Handling Patterns
```csharp
public async Task<[Entity]DetailsDto> CreateAsync(Create[Entity]Dto input)
{
    try
    {
        Logger.Info($"Creating new [Entity] with name: {input.Name}");
        
        // Validation
        await ValidateCreate[Entity]Input(input);
        
        // Business logic
        var entity = await Create[Entity]WithBusinessRules(input);
        
        Logger.Info($"Successfully created [Entity] with ID: {entity.Id}");
        
        return ObjectMapper.Map<[Entity]DetailsDto>(entity);
    }
    catch (BusinessException ex)
    {
        Logger.Warn($"Business rule violation during [Entity] creation: {ex.Message}");
        throw; // Re-throw business exceptions
    }
    catch (ValidationException ex)
    {
        Logger.Warn($"Validation failed during [Entity] creation: {ex.Message}");
        throw new UserFriendlyException(L("ValidationFailed"), ex);
    }
    catch (Exception ex)
    {
        Logger.Error($"Unexpected error during [Entity] creation: {ex.Message}", ex);
        throw new UserFriendlyException(L("UnexpectedErrorOccurred"));
    }
}
```

#### Audit Logging Implementation
```csharp
public async Task<[Entity]DetailsDto> UpdateAsync(Update[Entity]Dto input)
{
    var entity = await _[entity]Repository.GetAsync(input.Id);
    var originalState = ObjectMapper.Map<[Entity]AuditDto>(entity);
    
    // Update entity
    entity.Update(input.Name, input.TypeId, input.Description);
    
    // Log the change
    await _auditLogService.LogEntityChange(
        entity.Id,
        typeof([Entity]).Name,
        originalState,
        ObjectMapper.Map<[Entity]AuditDto>(entity)
    );
    
    await _[entity]Repository.UpdateAsync(entity);
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

### Integration Patterns

#### Domain Event Handling
```csharp
public class [Entity]ApprovedEventHandler : IAsyncEventHandler<[Entity]ApprovedEvent>
{
    private readonly INotificationService _notificationService;
    private readonly IBackgroundJobManager _backgroundJobManager;
    
    public async Task HandleEventAsync([Entity]ApprovedEvent eventData)
    {
        // Send notification
        await _notificationService.SendNotificationAsync(
            eventData.Entity.CreatorUserId,
            "[Entity]ApprovedNotification",
            new { EntityName = eventData.Entity.Name }
        );
        
        // Trigger background processes
        await _backgroundJobManager.EnqueueAsync<[Entity]PostApprovalJob>(
            job => job.ProcessApproval(eventData.Entity.Id)
        );
        
        // Update related entities
        await UpdateRelatedEntitiesAsync(eventData.Entity);
    }
}
```

#### External Service Integration
```csharp
public async Task<[Entity]DetailsDto> CreateWithExternalValidationAsync(Create[Entity]Dto input)
{
    // Validate with external service
    var validationResult = await _externalValidationService.ValidateAsync(input);
    if (!validationResult.IsValid)
    {
        throw new UserFriendlyException(validationResult.ErrorMessage);
    }
    
    // Create entity
    var entity = await CreateEntityAsync(input);
    
    // Sync with external system
    await _externalSyncService.SyncEntityAsync(entity);
    
    return ObjectMapper.Map<[Entity]DetailsDto>(entity);
}
```

## Analysis Checklist

Before generating DTOs, ensure you have:

1. ✅ **Identified all UI models** from `user_story.ui_models`
2. ✅ **Mapped all filter configurations** from `user_story.filters` 
3. ✅ **Extracted business rules** that affect field nullability and validation
4. ✅ **Documented domain enums** with their value mappings
5. ✅ **Determined entity context** for proper namespace
6. ✅ **Validated field types** based on UI column specifications

## Quality Assurance Framework

### Testing Implementation Requirements

#### Unit Testing Patterns
```csharp
[TestClass]
public class [Entity]AppService_Tests : AppTestBase
{
    private readonly I[Entity]AppService _[entity]AppService;
    private readonly I[Entity]CustomRepository _[entity]Repository;
    
    public [Entity]AppService_Tests()
    {
        _[entity]AppService = Resolve<I[Entity]AppService>();
        _[entity]Repository = Resolve<I[Entity]CustomRepository>();
    }
    
    [TestMethod]
    public async Task GetListAsync_Should_Return_Filtered_Results()
    {
        // Arrange
        await Create_Test_[Entity]_Data();
        var input = new Get[Entity]ListInput
        {
            SimpleSearch = "test",
            StatusIds = new List<int> { 1, 2 },
            MaxResultCount = 10
        };
        
        // Act
        var result = await _[entity]AppService.GetListAsync(input);
        
        // Assert
        result.TotalCount.ShouldBeGreaterThan(0);
        result.Items.ShouldNotBeNull();
        result.Items.All(x => x.Name.Contains("test")).ShouldBeTrue();
    }
    
    [TestMethod]
    public async Task CreateAsync_Should_Apply_Business_Rules()
    {
        // Arrange
        var input = new Create[Entity]Dto
        {
            Name = "Test Entity",
            TypeId = 1,
            Description = "Test Description"
        };
        
        // Act
        var result = await _[entity]AppService.CreateAsync(input);
        
        // Assert
        result.ShouldNotBeNull();
        result.Name.ShouldBe(input.Name);
        result.Id.ShouldBeGreaterThan(0);
    }
}
```

#### Integration Testing Requirements
```csharp
[TestClass]
public class [Entity]Integration_Tests : IntegrationTestBase
{
    [TestMethod]
    public async Task Complete_[Entity]_Workflow_Should_Work()
    {
        // Test complete workflow: Create -> Update -> Approve -> Archive
        
        // 1. Create
        var createInput = new Create[Entity]Dto { /* test data */ };
        var created = await PostAsync("/api/[entity]", createInput);
        
        // 2. Update
        var updateInput = new Update[Entity]Dto { /* updated data */ };
        var updated = await PutAsync($"/api/[entity]/{created.Id}", updateInput);
        
        // 3. Business operation
        var approved = await PostAsync($"/api/[entity]/{created.Id}/approve", null);
        
        // 4. Verify final state
        var final = await GetAsync($"/api/[entity]/{created.Id}");
        final.Status.ShouldBe(EntityStatus.Approved);
    }
}
```

### Performance Benchmarking

#### Repository Performance Tests
```csharp
[TestMethod]
public async Task GetListAsync_Performance_Should_Be_Under_Threshold()
{
    // Arrange: Create large dataset
    await Create_Large_Test_Dataset(10000);
    
    var input = new Get[Entity]ListInput
    {
        MaxResultCount = 100,
        SkipCount = 0
    };
    
    // Act: Measure execution time
    var stopwatch = Stopwatch.StartNew();
    var result = await _[entity]AppService.GetListAsync(input);
    stopwatch.Stop();
    
    // Assert: Performance criteria
    stopwatch.ElapsedMilliseconds.ShouldBeLessThan(1000); // Under 1 second
    result.TotalCount.ShouldBe(10000);
    result.Items.Count.ShouldBe(100);
}
```

### Code Quality Metrics

#### Complexity Assessment
- **Cyclomatic Complexity**: Maximum 10 per method
- **Lines of Code**: Maximum 50 lines per method
- **Dependencies**: Maximum 5 constructor dependencies per class
- **Test Coverage**: Minimum 85% line coverage for business logic

#### Architecture Compliance Validation
```csharp
public class Architecture_Compliance_Tests
{
    [TestMethod]
    public void Application_Services_Should_Not_Reference_EF_Core()
    {
        var applicationAssembly = typeof([Entity]AppService).Assembly;
        var efCoreReferences = applicationAssembly.GetReferencedAssemblies()
            .Where(a => a.Name.Contains("EntityFramework"));
        
        efCoreReferences.ShouldBeEmpty();
    }
    
    [TestMethod]
    public void DTOs_Should_Not_Have_Business_Logic()
    {
        var dtoTypes = typeof([Entity]ListItemDto).Assembly.GetTypes()
            .Where(t => t.Name.EndsWith("Dto"));
        
        foreach (var dtoType in dtoTypes)
        {
            dtoType.GetMethods()
                .Where(m => !m.IsSpecialName && m.DeclaringType == dtoType)
                .ShouldBeEmpty();
        }
    }
}
```

## Implementation Output Structure

### File Organization Pattern
```
📁 [USER_STORY_ID]_implementation/
├── 📄 1_DTOs/
│   └── [Entity]Dtos.cs
├── 📄 2_Application_Services/
│   ├── I[Entity]AppService.cs
│   └── [Entity]AppService.cs
├── 📄 3_Repositories/
│   ├── I[Entity]CustomRepository.cs
│   └── [Entity]CustomRepository.cs
├── 📄 4_AutoMapper/
│   └── [Entity]MapProfile.cs
├── 📄 5_Permissions/
│   └── [Entity]Permissions.cs (if needed)
├── 📄 6_Domain_Events/
│   ├── [Entity]Events.cs (if needed)
│   └── [Entity]EventHandlers.cs (if needed)
└── 📄 7_Tests/
    ├── [Entity]AppService_Tests.cs
    └── [Entity]Integration_Tests.cs
```

### Generated File Headers
```csharp
// File: [Entity]AppService.cs
// User Story: [USER_STORY_ID] - [TITLE_AR] ([TITLE_EN])
// Generated: [TIMESTAMP]
// Framework: ASP.NET Zero / ABP Framework
// Context: Thiqah.Mazad.[CONTEXT]
// 
// IMPORTANT: This implementation follows Mazad Backend API coding standards.
// All patterns and conventions are aligned with project instructions.
// 
// Business Rules Implemented:
// - [BR-1]: [DESCRIPTION]
// - [BR-2]: [DESCRIPTION]
// 
// Components Included:
// ✅ DTOs with validation
// ✅ Application service with authorization
// ✅ Custom repository with optimized queries
// ✅ AutoMapper profile (Entity -> DTO only)
// ✅ Permission definitions
// ✅ Unit and integration tests
```

## Build Verification and Error Resolution Framework

### **MANDATORY BUILD STEP**

After generating the complete implementation, you **MUST** execute the following build verification process:

#### 1. **Solution Build Execution**
```powershell
# Navigate to solution directory
cd "d:\work\MazadBackEnd"

# Clean previous builds
dotnet clean Thiqah.Mazad.Web.sln

# Restore NuGet packages
dotnet restore Thiqah.Mazad.Web.sln

# Build entire solution
dotnet build Thiqah.Mazad.Web.sln --configuration Debug --verbosity normal
```

#### 2. **Build Result Analysis**
```powershell
# Check build exit code
if ($LASTEXITCODE -eq 0) {
    Write-Host "✅ BUILD SUCCESSFUL - Implementation is ready for use"
} else {
    Write-Host "❌ BUILD FAILED - Error resolution required"
    # Proceed to error resolution framework
}
```

### **Error Resolution Framework**

#### **Phase 1: Error Detection and Categorization**

##### Compilation Error Categories
```csharp
// Category 1: Missing Using Statements
// Error Pattern: "The type or namespace name 'X' could not be found"
// Solution: Add missing using statements

// Category 2: Namespace Mismatches  
// Error Pattern: "The type 'X' exists in both 'Y' and 'Z'"
// Solution: Use fully qualified names or alias

// Category 3: Missing Dependencies
// Error Pattern: "The type 'X' is defined in an assembly that is not referenced"
// Solution: Add project references or NuGet packages

// Category 4: Interface Implementation Issues
// Error Pattern: "does not implement interface member"
// Solution: Implement missing interface members

// Category 5: Entity Framework Issues
// Error Pattern: "DbSet<T> does not contain a definition for"
// Solution: Update DbContext and add DbSet properties
```

#### **Phase 2: Automatic Error Resolution**

##### Common Error Fixes Implementation
```csharp
// Fix 1: Missing Using Statements
/*
Add these standard using statements to generated files:
using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.Linq;
using System.Threading.Tasks;
using Abp.Application.Services;
using Abp.Application.Services.Dto;
using Abp.Authorization;
using Abp.Domain.Repositories;
using Abp.EntityFrameworkCore;
using Abp.Linq.Extensions;
using Microsoft.EntityFrameworkCore;
using Thiqah.Mazad.Authorization;
using Thiqah.Mazad.Common.Dto;
*/

// Fix 2: Repository Registration
/*
Add to [Context]RepositoryModule.cs:
Configuration.Modules.AbpEfCore().AddDbContext<[Context]DbContext>(options =>
{
    options.DbContextOptions.UseSqlServer(options.ConnectionString);
});

IocManager.RegisterAssemblyByConvention(typeof([Context]RepositoryModule).GetAssembly());
*/

// Fix 3: AutoMapper Registration
/*
Add to [Context]ApplicationModule.cs:
Configuration.Modules.AbpAutoMapper().Configurators.Add(config =>
{
    config.AddProfile<[Entity]MapProfile>();
});
*/

// Fix 4: Permission Registration
/*
Add to AuthorizationProvider.cs:
context.CreatePermission(PermissionNames.Pages_[Entity], L("Pages_[Entity]"));
context.CreatePermission(PermissionNames.Pages_[Entity]_Create, L("Pages_[Entity]_Create"));
context.CreatePermission(PermissionNames.Pages_[Entity]_Update, L("Pages_[Entity]_Update"));
context.CreatePermission(PermissionNames.Pages_[Entity]_Delete, L("Pages_[Entity]_Delete"));
*/
```

#### **Phase 3: Iterative Resolution Process**

##### Error Resolution Loop
```powershell
# Iterative build and fix process
$maxAttempts = 5
$attempt = 1

do {
    Write-Host "🔄 Build Attempt $attempt of $maxAttempts"
    
    # Execute build
    dotnet build Thiqah.Mazad.Web.sln --configuration Debug
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✅ BUILD SUCCESSFUL after $attempt attempts"
        break
    }
    
    # Analyze errors and apply fixes
    Write-Host "🔧 Analyzing errors and applying fixes..."
    
    # Apply automatic fixes based on error patterns
    # 1. Add missing using statements
    # 2. Fix namespace references
    # 3. Implement missing interface members
    # 4. Update dependency registrations
    
    $attempt++
    
} while ($attempt -le $maxAttempts)

if ($LASTEXITCODE -ne 0) {
    Write-Host "❌ BUILD FAILED after $maxAttempts attempts - Manual intervention required"
    # Generate detailed error report
}
```

### **Specific Error Resolution Patterns**

#### **Entity Framework Errors**
```csharp
// Error: DbSet not found
// Fix: Add to appropriate DbContext
public virtual DbSet<[Entity]> [Entity]s { get; set; }

// Error: Migration required
// Fix: Add migration commands
/*
Add-Migration Add[Entity]Entity -Context [Context]DbContext
Update-Database -Context [Context]DbContext
*/
```

#### **Dependency Injection Errors**
```csharp
// Error: Service not registered
// Fix: Add to dependency registration
public override void PreInitialize()
{
    IocManager.Register<I[Entity]CustomRepository, [Entity]CustomRepository>(DependencyLifeStyle.Transient);
    IocManager.Register<I[Entity]AppService, [Entity]AppService>(DependencyLifeStyle.Transient);
}
```

#### **AutoMapper Configuration Errors**
```csharp
// Error: Missing mapping configuration
// Fix: Ensure proper profile registration
[DependsOn(typeof(AbpAutoMapperModule))]
public class [Context]ApplicationModule : AbpModule
{
    public override void PreInitialize()
    {
        Configuration.Modules.AbpAutoMapper().Configurators.Add(config =>
        {
            // Add custom mappings
            config.AddProfile<[Entity]MapProfile>();
        });
    }
}
```

#### **Permission System Errors**
```csharp
// Error: Permission not found
// Fix: Add to PermissionNames.cs
public const string Pages_[Entity] = "Pages.[Entity]";
public const string Pages_[Entity]_Create = "Pages.[Entity].Create";
public const string Pages_[Entity]_Update = "Pages.[Entity].Update";
public const string Pages_[Entity]_Delete = "Pages.[Entity].Delete";

// Fix: Add to AuthorizationProvider.cs
var [entity]Permission = context.CreatePermission(PermissionNames.Pages_[Entity], L("Pages_[Entity]"));
[entity]Permission.CreateChildPermission(PermissionNames.Pages_[Entity]_Create, L("Pages_[Entity]_Create"));
[entity]Permission.CreateChildPermission(PermissionNames.Pages_[Entity]_Update, L("Pages_[Entity]_Update"));
[entity]Permission.CreateChildPermission(PermissionNames.Pages_[Entity]_Delete, L("Pages_[Entity]_Delete"));
```

### **Build Quality Gates**

#### **Mandatory Success Criteria**
```powershell
# 1. Zero Compilation Errors
if ((dotnet build --verbosity quiet 2>&1 | Select-String "error").Count -eq 0) {
    Write-Host "✅ No compilation errors found"
} else {
    Write-Host "❌ Compilation errors must be resolved"
    exit 1
}

# 2. Warning Threshold Check
$warningCount = (dotnet build --verbosity normal 2>&1 | Select-String "warning").Count
if ($warningCount -le 5) {
    Write-Host "✅ Warning count ($warningCount) within acceptable threshold"
} else {
    Write-Host "⚠️ High warning count ($warningCount) - Review recommended"
}

# 3. Project-Specific Build Verification
dotnet build src/Thiqah.Mazad.Core/Thiqah.Mazad.Core.csproj
dotnet build src/Thiqah.Mazad.Application/Thiqah.Mazad.Application.csproj
dotnet build src/Thiqah.Mazad.EntityFrameworkCore/Thiqah.Mazad.EntityFrameworkCore.csproj
```

#### **Post-Build Validation**
```powershell
# Validate generated assemblies
$coreAssembly = "src/Thiqah.Mazad.Core/bin/Debug/net6.0/Thiqah.Mazad.Core.dll"
$appAssembly = "src/Thiqah.Mazad.Application/bin/Debug/net6.0/Thiqah.Mazad.Application.dll"

if (Test-Path $coreAssembly -and Test-Path $appAssembly) {
    Write-Host "✅ All required assemblies generated successfully"
} else {
    Write-Host "❌ Missing required assemblies - Build verification failed"
}
```

### **Error Resolution Documentation**

#### **Build Log Analysis**
```powershell
# Generate comprehensive build report
dotnet build Thiqah.Mazad.Web.sln --verbosity diagnostic > build_report.txt

# Extract key information
$errors = Select-String -Path "build_report.txt" -Pattern "error"
$warnings = Select-String -Path "build_report.txt" -Pattern "warning"

Write-Host "📊 Build Summary:"
Write-Host "   Errors: $($errors.Count)"
Write-Host "   Warnings: $($warnings.Count)"

# Generate fix recommendations
foreach ($error in $errors) {
    Write-Host "🔧 Error: $($error.Line)"
    # Apply pattern matching for automatic fix suggestions
}
```

## Final Implementation Checklist

### ✅ **Build Verification (MANDATORY)**
- [ ] **Solution Build**: Clean build of entire solution completed successfully
- [ ] **Zero Errors**: No compilation errors in any project
- [ ] **Warning Threshold**: Warnings kept under acceptable limit (≤5)
- [ ] **Assembly Generation**: All required assemblies generated correctly
- [ ] **Error Resolution**: All build errors automatically resolved
- [ ] **Quality Gates**: Build quality criteria met
- [ ] **Dependency Check**: All project references and NuGet packages resolved
- [ ] **Integration Validation**: Generated code integrates seamlessly with existing codebase

### ✅ **Mandatory Compliance**
- [ ] **Repository Pattern**: All GetListAsync methods return `(int totalCount, List<Entity> entities)`
- [ ] **Application Services**: No EF Core code, repository calls only
- [ ] **AutoMapper**: Entity to DTO mapping only, never reverse
- [ ] **Naming Conventions**: All patterns follow project standards
- [ ] **Inheritance**: Proper base class usage (PagedAndSortedInputDto, etc.)

### ✅ **Architecture Compliance**
- [ ] **Bounded Context**: Proper namespace and context separation
- [ ] **Domain Integrity**: Business rules implemented in domain layer
- [ ] **Separation of Concerns**: Clear layer boundaries maintained
- [ ] **Dependency Direction**: Dependencies point inward to domain

### ✅ **Quality Standards**
- [ ] **Performance**: Optimized database queries and memory usage
- [ ] **Security**: Authorization and input validation implemented
- [ ] **Error Handling**: Comprehensive exception handling with logging
- [ ] **Testing**: Unit and integration tests included

### ✅ **Business Requirements**
- [ ] **All Business Rules**: Every BR-X rule implemented correctly
- [ ] **UI Models**: All نموذج-XXX patterns supported
- [ ] **Filters**: Complete filter implementation based on user story
- [ ] **Validation**: Input validation aligned with business requirements

### ✅ **Project Integration**
- [ ] **Minimal Changes**: Only required modifications made
- [ ] **Backwards Compatibility**: Existing functionality preserved
- [ ] **Code Standards**: Follows established project patterns
- [ ] **Documentation**: Comprehensive inline documentation

### ✅ **Final Build Verification**
- [ ] **Clean Build**: Solution builds without errors after implementation
- [ ] **No Warnings**: Critical warnings resolved, total warnings ≤5
- [ ] **Assembly Integrity**: All generated assemblies are valid and loadable
- [ ] **Runtime Verification**: Application starts successfully with new implementation
- [ ] **Integration Testing**: Generated code passes integration tests
- [ ] **Performance Impact**: No significant performance degradation introduced

**CRITICAL SUCCESS CRITERIA**: Implementation is considered complete ONLY when the solution builds successfully with zero compilation errors and passes all quality gates.

**Generate complete, production-ready implementation following this comprehensive framework while maintaining strict adherence to project standards, minimal changes approach, AND ensuring successful compilation through the mandatory build verification process.**
