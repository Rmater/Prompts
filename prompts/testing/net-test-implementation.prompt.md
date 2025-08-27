# .NET Test Implementation Prompt

## Context

### Overview
This prompt guides the implementation of missing test cases for the Mazad Backend API, a legal auction system built with ASP.NET Core and the ASP.NET Zero framework. The testing strategy should align with the project's domain-driven design architecture and cover all critical business logic.

### Project Architecture
- **Domain Layer**: Core business logic and entities
- **Application Layer**: Use case orchestration and DTOs
- **Infrastructure Layer**: External integrations and data access
- **API Layer**: REST endpoints and controllers

## Test Implementation Guidelines

### 1. Test Coverage Analysis

#### Initial Assessment
1. Review existing test coverage reports from SonarQube
2. Identify critical business domains lacking coverage:
   - Auction management and bidding logic
   - Wallet transactions and financial calculations
   - User authentication and authorization
   - Real-time notifications and SignalR hubs
   - Court integration and multi-tenancy features

#### Coverage Priorities
1. Domain Services and Business Rules
2. Application Services and Use Cases
3. Infrastructure Services and Integrations
4. API Controllers and Endpoints

### 2. Test Categories

#### 2.1 Unit Tests
```csharp
[TestClass]
public class AuctionDomainServiceTests
{
    private readonly ITestOutputHelper _output;
    private readonly Mock<IAuctionRepository> _auctionRepository;
    private readonly Mock<IBidderRepository> _bidderRepository;
    private readonly AuctionDomainService _sut;

    public AuctionDomainServiceTests(ITestOutputHelper output)
    {
        _output = output;
        _auctionRepository = new Mock<IAuctionRepository>();
        _bidderRepository = new Mock<IBidderRepository>();
        _sut = new AuctionDomainService(
            _auctionRepository.Object,
            _bidderRepository.Object
        );
    }

    [TestMethod]
    [DataRow("Active", 1000, true)]
    [DataRow("Closed", 1000, false)]
    public async Task CanBidderParticipate_WithValidState_ReturnsExpectedResult(
        string auctionStatus, 
        decimal bidAmount,
        bool expected)
    {
        // Arrange
        var auction = new Auction { Status = auctionStatus };
        var bidder = new Bidder { /* setup test data */ };

        // Act
        var result = await _sut.CanBidderParticipateAsync(auction, bidder);

        // Assert
        Assert.AreEqual(expected, result);
        _output.WriteLine($"Test completed for status: {auctionStatus}");
    }
}
```

Focus Areas:
1. **Domain Logic**
   - Business rules validation
   - Entity state transitions
   - Value object operations
   - Domain event handling

2. **Application Services**
   - Use case workflows
   - DTO mapping
   - Authorization checks
   - Input validation

3. **Infrastructure Services**
   - Repository implementations
   - External service integrations
   - Caching mechanisms
   - Background job execution

#### 2.2 Integration Tests
```csharp
[TestClass]
public class AuctionIntegrationTests : TestBase
{
    private readonly ITestOutputHelper _output;
    private readonly IAuctionAppService _auctionAppService;
    private readonly IWalletAppService _walletAppService;

    public AuctionIntegrationTests(ITestOutputHelper output)
    {
        _output = output;
        _auctionAppService = Resolve<IAuctionAppService>();
        _walletAppService = Resolve<IWalletAppService>();
    }

    [TestMethod]
    public async Task PlaceBid_WithSufficientFunds_ShouldSucceed()
    {
        // Arrange
        await WithUnitOfWorkAsync(async () =>
        {
            var auction = await CreateTestAuction();
            var bidder = await CreateTestBidderWithFunds();

            // Act
            var result = await _auctionAppService.PlaceBidAsync(new PlaceBidDto
            {
                AuctionId = auction.Id,
                BidAmount = 1000m
            });

            // Assert
            Assert.IsNotNull(result);
            var updatedWallet = await _walletAppService.GetWalletAsync(bidder.Id);
            Assert.AreEqual(expectedReservedAmount, updatedWallet.ReservedAmount);
        });
    }
}
```

Focus Areas:
1. **Cross-Service Workflows**
   - End-to-end auction processes
   - Payment processing flows
   - Multi-service transactions
   - Event propagation

2. **Database Integration**
   - Entity Framework operations
   - Transaction management
   - Concurrency handling
   - Data consistency

3. **External Systems**
   - Payment gateway integration
   - SMS/Email service calls
   - File storage operations
   - Court system integration

#### 2.3 API Tests
```csharp
[TestClass]
public class AuctionApiTests : WebTestBase
{
    private readonly ITestOutputHelper _output;

    public AuctionApiTests(ITestOutputHelper output)
    {
        _output = output;
    }

    [TestMethod]
    public async Task GetAuction_WithValidId_ReturnsCorrectAuction()
    {
        // Arrange
        var client = await GetAuthenticatedClientAsync("admin");
        var auction = await CreateTestAuctionAsync();

        // Act
        var response = await client.GetAsync($"/api/services/app/Auction/Get?id={auction.Id}");
        
        // Assert
        response.EnsureSuccessStatusCode();
        var result = await response.Content.ReadAsAsync<AuctionDto>();
        Assert.AreEqual(auction.Id, result.Id);
    }
}
```

Focus Areas:
1. **HTTP Endpoints**
   - Request/response validation
   - Authentication/authorization
   - Error handling
   - Rate limiting

2. **Real-time Features**
   - SignalR hub testing
   - WebSocket connections
   - Live bidding scenarios
   - Notification delivery

### 3. Test Data Management

#### 3.1 Test Data Builders
```csharp
public class TestAuctionBuilder
{
    private readonly Auction _auction;

    public TestAuctionBuilder()
    {
        _auction = new Auction
        {
            Title = "Test Auction",
            Status = AuctionStatus.Active,
            StartDate = Clock.Now.AddDays(1),
            EndDate = Clock.Now.AddDays(2),
            MinimumBidPrice = 100m
        };
    }

    public TestAuctionBuilder WithStatus(AuctionStatus status)
    {
        _auction.Status = status;
        return this;
    }

    public TestAuctionBuilder WithBids(params AuctionBid[] bids)
    {
        foreach (var bid in bids)
        {
            _auction.AddBid(bid);
        }
        return this;
    }

    public Auction Build() => _auction;
}
```

#### 3.2 Test Data Categories
1. **Valid Test Data**
   - Typical business scenarios
   - Edge cases within valid ranges
   - Different entity states

2. **Invalid Test Data**
   - Boundary violations
   - Business rule violations
   - Invalid state transitions

3. **Special Cases**
   - Multi-tenant scenarios
   - Concurrent operations
   - Time-sensitive operations

### 4. Testing Best Practices

#### 4.1 Test Structure
1. **Arrange**
   - Set up test dependencies
   - Create test data
   - Configure mocks

2. **Act**
   - Execute the system under test
   - Capture exceptions
   - Record timing if relevant

3. **Assert**
   - Verify expected outcomes
   - Check state changes
   - Validate side effects

#### 4.2 Mock Guidelines
```csharp
// Repository mocking
_auctionRepository
    .Setup(r => r.GetAsync(It.IsAny<long>()))
    .ReturnsAsync((long id) => new Auction { Id = id });

// External service mocking
_paymentGateway
    .Setup(p => p.ProcessPaymentAsync(
        It.IsAny<decimal>(), 
        It.IsAny<string>()))
    .ReturnsAsync(new PaymentResult { Success = true });
```

1. **Mock Configuration**
   - Mock only external dependencies
   - Set up realistic behaviors
   - Handle async operations correctly

2. **Verification**
   - Verify critical interactions
   - Check call counts
   - Validate parameter values

#### 4.3 Test Categories (Attributes)
```csharp
[TestCategory("Unit")]
[TestCategory("Domain")]
[TestCategory("AuctionManagement")]
public class AuctionTests
{
    [TestMethod]
    [TestCategory("BusinessRules")]
    [TestCategory("Critical")]
    public void PlaceBid_ValidatesMinimumIncrement()
    {
        // Test implementation
    }
}
```

### 5. Implementation Strategy

#### 5.1 Priority Order
1. Critical business logic in domain layer
2. Core application services
3. Integration points with external systems
4. API endpoints and controllers
5. Background jobs and automation

#### 5.2 Implementation Steps
1. **For Each Missing Test Area**
   ```
   a. Identify test scenarios
   b. Create test data builders
   c. Implement test cases
   d. Verify coverage
   e. Document special cases
   ```

2. **For Each Test Case**
   ```
   a. Write test method
   b. Set up test data
   c. Implement assertions
   d. Run and verify
   e. Add to test suite
   ```

### 6. Quality Assurance

#### 6.1 Code Review Checklist
- [ ] Tests follow naming conventions
- [ ] Appropriate test categories applied
- [ ] Mock setup is realistic
- [ ] Assertions are comprehensive
- [ ] Error cases are covered
- [ ] Documentation is clear
- [ ] No test data is hardcoded
- [ ] Performance considerations addressed

#### 6.2 Coverage Goals
- Domain Layer: 90%+ coverage
- Application Layer: 85%+ coverage
- Infrastructure Layer: 80%+ coverage
- API Layer: 75%+ coverage

### 7. Documentation

#### 7.1 Test Documentation
```csharp
/// <summary>
/// Validates that a bid can only be placed on an active auction
/// with sufficient funds and within the minimum increment rules.
/// </summary>
/// <remarks>
/// Test cases:
/// 1. Valid bid with sufficient funds
/// 2. Invalid bid below minimum increment
/// 3. Invalid bid with insufficient funds
/// 4. Invalid bid on inactive auction
/// </remarks>
[TestMethod]
public async Task PlaceBid_ValidatesAllBusinessRules()
{
    // Test implementation
}
```

#### 7.2 Required Documentation Elements
1. Purpose of test case
2. Preconditions and setup
3. Test data explanation
4. Expected outcomes
5. Special considerations
6. Related business rules

### 8. Maintenance

#### 8.1 Regular Review Tasks
1. Update tests for new features
2. Refactor tests for changed functionality
3. Remove obsolete tests
4. Update test data and mocks
5. Verify coverage metrics

#### 8.2 Performance Monitoring
1. Track test execution time
2. Optimize slow running tests
3. Review resource usage
4. Monitor CI/CD impact

## Example Implementation

### Complete Test Class Example
```csharp
[TestClass]
public class AuctionBiddingTests : TestBase
{
    private readonly ITestOutputHelper _output;
    private readonly IAuctionManager _auctionManager;
    private readonly IWalletManager _walletManager;
    private readonly TestAuctionBuilder _auctionBuilder;
    private readonly TestBidderBuilder _bidderBuilder;

    public AuctionBiddingTests(ITestOutputHelper output)
    {
        _output = output;
        _auctionManager = Resolve<IAuctionManager>();
        _walletManager = Resolve<IWalletManager>();
        _auctionBuilder = new TestAuctionBuilder();
        _bidderBuilder = new TestBidderBuilder();
    }

    [TestMethod]
    [TestCategory("BusinessRules")]
    [TestCategory("Critical")]
    [DataRow(1000, 1200, true)]  // Valid increment
    [DataRow(1000, 1050, false)] // Below minimum increment
    public async Task PlaceBid_ValidatesMinimumIncrementRules(
        decimal currentBid,
        decimal newBid,
        bool shouldSucceed)
    {
        // Arrange
        var auction = await WithUnitOfWorkAsync(async () =>
        {
            var auction = _auctionBuilder
                .WithStatus(AuctionStatus.Active)
                .WithCurrentBid(currentBid)
                .WithMinimumIncrement(100)
                .Build();
                
            return await _auctionManager.CreateAsync(auction);
        });

        var bidder = await WithUnitOfWorkAsync(async () =>
        {
            var bidder = _bidderBuilder
                .WithWalletBalance(2000)
                .Build();
                
            return await _walletManager.CreateAsync(bidder);
        });

        // Act & Assert
        await WithUnitOfWorkAsync(async () =>
        {
            if (shouldSucceed)
            {
                var result = await _auctionManager.PlaceBidAsync(
                    auction.Id,
                    bidder.Id,
                    newBid);
                    
                Assert.IsTrue(result.Success);
                Assert.AreEqual(newBid, result.NewBidAmount);
            }
            else
            {
                await Assert.ThrowsExceptionAsync<BusinessException>(
                    async () => await _auctionManager.PlaceBidAsync(
                        auction.Id,
                        bidder.Id,
                        newBid));
            }
        });
    }
}
```

## Conclusion

This prompt provides a comprehensive framework for implementing missing test cases in the Mazad Backend API. Following these guidelines ensures:

1. Systematic coverage of all critical functionality
2. Consistent test quality and maintainability
3. Clear documentation and maintainable test code
4. Efficient test execution and reliable results

Remember to adapt these guidelines based on specific project requirements while maintaining the core testing principles and quality standards.
