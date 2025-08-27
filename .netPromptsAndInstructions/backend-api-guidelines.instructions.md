# Backend API - Coding Guidelines

## ASP.NET Zero Framework Patterns

### Application Layer
- **Application Services**: Use for coordinating business operations across multiple domain entities
- **DTOs (Data Transfer Objects)**: Create specific DTOs for each operation to ensure API contract stability
- **AutoMapper Profiles**: Define mapping configurations between entities and DTOs
- **Permission Checking**: Use ABP's permission system for authorization at application service level

### Domain Layer
- **Entities**: Follow ABP entity conventions with proper audit properties
- **Domain Services**: Implement business logic that doesn't belong to a single entity
- **Specifications**: Use specification pattern for complex query logic
- **Domain Events**: Publish events for important business operations

### Repository Pattern
- **Custom Repositories**: Extend ABP's base repository for complex queries
- **Specifications**: Use for building complex, reusable query logic
- **Unit of Work**: Leverage ABP's automatic UoW for transaction management
- **Soft Delete**: Use ABP's soft delete for maintaining data integrity

## Database and Entity Management

### Entity Framework Core
- **Migrations**: Use code-first migrations with proper naming conventions
- **Fluent API**: Configure complex relationships and constraints in DbContext
- **Indexes**: Create appropriate indexes for query performance
- **Audit Properties**: Leverage ABP's automatic audit property population

### Performance Optimization
- **Lazy Loading**: Use judiciously to avoid N+1 query problems
- **Eager Loading**: Include related entities when needed upfront
- **Query Optimization**: Use projection with Select to load only needed data
- **Bulk Operations**: Use bulk insert/update for large datasets

## Auction-Specific Business Logic

### Bidding System
- **Concurrency Control**: Handle concurrent bid submissions with optimistic locking
- **Business Rules**: Implement bid increment validation and reserve price checks
- **Proxy Bidding**: Automatic bidding logic with maximum bid limits
- **Real-time Updates**: SignalR integration for live bid updates

### Auction Management
- **State Machine**: Implement auction status transitions with proper validation
- **Scheduling**: Use Hangfire for auction start/end automation
- **Commission Calculation**: Configurable buyer's premium and seller's commission
- **Invoice Generation**: Automatic invoice creation after auction close

### Multi-tenancy Considerations
- **Tenant Isolation**: Ensure data separation between auction houses
- **Configuration**: Per-tenant settings for commission rates, terms, etc.
- **Custom Branding**: Support tenant-specific branding and terminology
- **Feature Toggles**: Enable/disable features per tenant

## API Design Patterns

### RESTful APIs
- **HTTP Verbs**: Use appropriate verbs (GET, POST, PUT, DELETE) for operations
- **Status Codes**: Return meaningful HTTP status codes
- **Pagination**: Implement consistent pagination for list endpoints
- **Filtering**: Support query parameters for filtering and sorting

### Error Handling
- **Exception Handling**: Use ABP's exception handling with custom exceptions
- **Validation**: Implement both client and server-side validation
- **Error Responses**: Consistent error response format across all endpoints
- **Logging**: Comprehensive logging for debugging and monitoring

### Authentication and Authorization
- **JWT Tokens**: Implement secure token-based authentication
- **Permission System**: Use ABP's fine-grained permission system
- **Role Management**: Implement hierarchical roles for different user types
- **Multi-factor Authentication**: Support for enhanced security

## Background Services and Jobs

### Hangfire Integration
- **Recurring Jobs**: Schedule regular tasks (auction notifications, reports)
- **Fire-and-Forget**: Queue immediate tasks (email sending, image processing)
- **Delayed Jobs**: Schedule future tasks (auction start/end automation)
- **Job Monitoring**: Implement proper error handling and retry logic

### Event-Driven Architecture
- **Domain Events**: Publish events for important business operations
- **Event Handlers**: Implement handlers for cross-cutting concerns
- **Integration Events**: Handle communication between bounded contexts
- **Event Store**: Consider event sourcing for audit requirements

## Performance and Scalability

### Caching Strategies
- **Memory Caching**: Cache frequently accessed data (auction details, user permissions)
- **Distributed Caching**: Use Redis for multi-instance deployments
- **Cache Invalidation**: Implement proper cache invalidation strategies
- **HTTP Caching**: Set appropriate cache headers for static data

### Database Optimization
- **Connection Pooling**: Configure appropriate connection pool sizes
- **Query Optimization**: Use Entity Framework query analysis tools
- **Bulk Operations**: Implement efficient bulk insert/update operations
- **Read Replicas**: Consider read replicas for reporting queries

## Security Best Practices

### Data Protection
- **Input Validation**: Validate all inputs at API boundaries
- **SQL Injection**: Use parameterized queries and avoid dynamic SQL
- **XSS Prevention**: Sanitize output and use proper encoding
- **Sensitive Data**: Encrypt sensitive data at rest and in transit

### API Security
- **CORS Configuration**: Proper CORS setup for frontend applications
- **Rate Limiting**: Implement rate limiting to prevent abuse
- **Request Validation**: Validate request size, content type, and format
- **Security Headers**: Set appropriate security headers in responses

## Testing Strategies

### Unit Testing
- **Domain Logic**: Test business rules and domain services thoroughly
- **Application Services**: Test application service logic with mocked dependencies
- **Repository Tests**: Test custom repository methods with in-memory database
- **Test Data Builders**: Use builder pattern for creating test data

### Integration Testing
- **API Testing**: Test complete request/response cycles
- **Database Testing**: Test with actual database for complex queries
- **SignalR Testing**: Test real-time functionality with test clients
- **Background Job Testing**: Test job execution and scheduling

## Monitoring and Observability

### Logging
- **Structured Logging**: Use structured logging with Serilog
- **Log Levels**: Use appropriate log levels (Debug, Info, Warning, Error)
- **Correlation IDs**: Track requests across service boundaries
- **Performance Logging**: Log slow queries and operations

### Health Checks
- **Database Health**: Monitor database connectivity and performance
- **External Services**: Check health of third-party integrations
- **Background Jobs**: Monitor job queue health and processing times
- **Memory and CPU**: Track resource utilization
