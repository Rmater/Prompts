# Work Item Implementation Guide
- i will provide you with work item details file in the prompt, use this file to build the implementation plan
- i want you to create the implementation plan file
- only create plan file, don't make any other changes or implementations
- Plan file Name: `<work-item-id>-implementaion-plan-<work-item-title-in-kebab-case>`
- Save the implementation plan file in the `Implementation Plans/{work-item-id}` folder
- respond in english 

## Workspace Projects Overview

The Mazad platform consists of the following projects:

- **MazadBackEnd**: Core API backend providing business logic, data management, and integrations for the Saudi Legal Auction System
- **MazadFrontEnd**: Auction house administrative portal for managing auctions, lots, bidders, and day-to-day auction operations
- **MazadPublicFrontEnd**: Customer-facing auction portal where public users browse, register, and participate in auctions
- **MazadSuperAdminFront**: Administrative portal for system-wide management and oversight of the Mazad auction platform
- **MazadSalesAgentFrontend**: Refactored version of MazadFrontEnd - Sales agent portal for managing multiple bidders and auction participation on behalf of clients

## Implementation Planning

>>> **MUST analyze all codebase before creating implementation plan** <<<

   - Required changes by project
      - Frontend Implementation (UI design and integration with backend)
         - Specify which frontend project(s) will be affected
         - Detail UI components and pages to be created/modified
         - **Use shared components** to prevent code duplication (table components, file upload components, modal components, form components, etc.)
         - Identify reusable components that can be shared across multiple frontend projects
         - Integration points with backend APIs

      - Backend services: 
         - Entities:
            - What is new: List new entities with names and key properties
            - What will be updated: List existing entities and what properties/methods will be modified
         - Repositories:
            - What is new: List new repositories with names and key methods
            - What will be updated: List existing repositories and what methods will be added/modified
         - DTOs (Data Transfer Objects):
            - What is new: List new DTOs with names and purpose
            - What will be updated: List existing DTOs and what properties will be added/modified
         - App Services:
            - What is new: List new app services with names and endpoint names
            - What will be updated: List existing app services and what methods/endpoints will be modified
         - External integration endpoints: List external APIs/services to be integrated (payment gateways, SMS services, etc.)
         - Database changes:
            - New entities: List new database entities to be created
            - Updated entities: List existing entities and what will be updated/modified

## Output Template Structure

Use the following structure for the implementation plan output:

```markdown
# Implementation Plan: [Work Item Title]

## Work Item Details
- **ID**: [Work Item ID]
- **Title**: [Work Item Title]
- **Description**: [Brief description of the requirement]

## Projects Affected
- [ ] MazadBackEnd
- [ ] MazadFrontEnd
- [ ] MazadPublicFrontEnd
- [ ] MazadSuperAdminFront
- [ ] MazadSalesAgentFrontend

## Frontend Implementation

### [Project Name 1] (if affected)
#### New Components
- Component Name: Purpose and functionality
- Component Name: Purpose and functionality

#### Modified Components
- Component Name: What will be changed
- Component Name: What will be changed

#### Shared Components to Create/Update
- Component Name: Reusable functionality across projects
- Component Name: Reusable functionality across projects

#### Pages/Routes
- Page/Route: Functionality and integration points
- Page/Route: Functionality and integration points

### [Project Name 2] (if affected)
[Same structure as above]

## Backend Implementation

### Entities
#### New Entities
- **EntityName**: Key properties and relationships
- **EntityName**: Key properties and relationships

#### Updated Entities
- **EntityName**: Properties/methods to be modified
- **EntityName**: Properties/methods to be modified

### Repositories
#### New Repositories
- **RepositoryName**: Key methods and purpose
- **RepositoryName**: Key methods and purpose

#### Updated Repositories
- **RepositoryName**: Methods to be added/modified
- **RepositoryName**: Methods to be added/modified

### DTOs (Data Transfer Objects)
#### New DTOs
- **DtoName**: Purpose and key properties
- **DtoName**: Purpose and key properties

#### Updated DTOs
- **DtoName**: Properties to be added/modified
- **DtoName**: Properties to be added/modified

### App Services
#### New App Services
- **ServiceName**: Endpoints and functionality
  - `GET /api/endpoint-name`: Description
  - `POST /api/endpoint-name`: Description
- **ServiceName**: Endpoints and functionality

#### Updated App Services
- **ServiceName**: Methods/endpoints to be modified
  - `PUT /api/endpoint-name`: What will be changed
  - `DELETE /api/endpoint-name`: What will be changed

### External Integrations
- **Integration Name**: External API/service details
- **Integration Name**: External API/service details

### Database Changes
#### New Tables/Entities
- **TableName**: Columns and relationships
- **TableName**: Columns and relationships

#### Table Modifications
- **TableName**: Columns to be added/modified/removed
- **TableName**: Columns to be added/modified/removed

## Implementation Steps
1. **Phase 1**: Database changes and entity updates
2. **Phase 2**: Repository and service layer implementation
3. **Phase 3**: API endpoints and DTOs
4. **Phase 4**: Frontend components and pages
5. **Phase 5**: Integration and testing

## Notes and Considerations
- Any special considerations or dependencies
- Performance implications
- Security considerations
- Testing requirements
```
