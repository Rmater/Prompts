# Angular Development Guidelines

## Project Architecture

### Module Organization
- Follow **feature-based module organization** to maintain code separation
- Each business domain feature should have its own module (e.g., ArgeementModule, PlatfomModule)
- Lazy-load feature modules to optimize initial load time
- Use shared modules for common components, directives, pipes, and services

### Feature Module Structure
- All feature modules must follow this consistent folder structure:  ```
  feature-name/
  ├── pages/                     # Dumb/presentational components
  │   ├── feature-form/
  │   │   ├── feature-form.component.html
  │   │   ├── feature-form.component.scss
  │   │   └── feature-form.component.ts
  │   └── feature-item/
  │       ├── feature-item.component.html
  │       ├── feature-item.component.scss
  │       └── feature-item.component.ts
  ├── containers/                     # Smart components
  │   ├── feature-list/
  │   │   ├── feature-list.component.html
  │   │   ├── feature-list.component.scss
  │   │   └── feature-list.component.ts
  │   └── feature-detail/
  │       ├── feature-detail.component.html
  │       ├── feature-detail.component.scss
  │       └── feature-detail.component.ts
  ├── models/                         # Interfaces and types
  │   └── feature.interface.ts
  ├── services/                       # API services
  │   └── feature.service.ts
  ├── store/                          # NgRx store (if applicable)
  │   ├── actions/
  │   │   └── feature.actions.ts
  │   ├── effects/
  │   │   └── feature.effects.ts
  │   ├── reducers/
  │   │   └── feature.reducer.ts
  │   ├── selectors/
  │   │   └── feature.selectors.ts
  │   └── index.ts
  ├── utils/                          # Helper functions
  │   └── feature.utils.ts
  ├── feature-routing.module.ts       # Routing config
  └── feature.module.ts               # Module definition
  ```
- Each feature module must have its own routing module for lazy loading
- Follow established patterns from existing modules (e.g., agreements, platforms)


### Naming Conventions
- **Files**: kebab-case (e.g., `platforms-list.component.ts`)
- **Classes**: PascalCase (e.g., `PlatformsListComponent`)
- **Variables/Properties**: camelCase (e.g., `platformsList`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `PLATFORMS_LIST`)
- **Interfaces**: PascalCase with 'I' prefix (e.g., `IPlatforms`)
- **Enums**: PascalCase (e.g., `PlatformsEnum`)
- **Observables**: Suffix with '$' (e.g., `platform$`)


### File Organization
- One component/service/directive per file
- Group related files in feature folders
- Maximum 400 lines per file (prefer smaller files)
- Core functionality belongs in `core` module
- Reusable components go in `shared` module
- add line space between methods

### Code Quality
- Write self-documenting code with descriptive names
- Use strong typing with TypeScript and avoid `any`
- Add JSDoc comments for public APIs and complex logic
- Optimize templates for Angular's change detection
- Use pure functions where possible

### Dependencies and Component Usage
- Do not install any new external libraries or npm packages
- Use only existing libraries and components already in the project
- Reuse components from the `core` and `shared` modules
- Extend existing functionality rather than duplicating it
- If a component does not exist, create a new one following project patterns


## Project-Specific Guidelines

### UI Styles and Components
- Follow the existing visual design system as seen in current modules
- Ensure all new pages maintain visual consistency with existing modules
- Use RTL-appropriate layouts with Arabic text alignment

### Responsive Design
- Use mobile-first approach with Bootstrap grid system
- Test on multiple device sizes and orientations
- Ensure critical functions work on all supported devices
- Implement touch-friendly UI elements for mobile
- Use responsive images and optimize assets for mobile


## Performance Optimization

### Change Detection
- Use `OnPush` change detection strategy
- Avoid direct DOM manipulation
- Implement `trackBy` functions for `ngFor` directives
- Use pure pipes instead of methods in templates
- Unsubscribe from observables in `ngOnDestroy`

### Rendering Optimization
- Lazy load modules for better initial load time
- Implement virtual scrolling for large lists using `ngx-virtual-scroller`
- Use Web Workers for CPU-intensive tasks
- Optimize bundle size with Tree Shaking
- Implement proper loading states and skeleton screens


## Working with the Backend

### API Communication
- Use strongly typed request/response models
- Implement retry logic for transient failures
- Structure API services by domain/feature
- Use interceptors for common request/response handling
- Handle loading, error, and success states consistently


## Forms and Validation

### Form Strategies
- Use Reactive Forms for complex forms with validation
- Implement form models with FormBuilder
- Create reusable form controls and validation
- Apply validation both client and server-side
- Use form arrays for dynamic form elements

### Validation
- Create custom validators for business rules
- Display validation errors consistently
- Implement cross-field validation where needed
- Disable submit buttons until form is valid
- Provide clear error messages to users


## Localization and Internationalization

### Text Management
- Store all user-facing text in resource files
- Use Angular i18n or ngx-translate
- Support RTL languages with proper CSS
- Format dates, numbers, and currencies based on locale
- Test UI with different languages to ensure layout stability


## Security Best Practices

### Authentication
- Implement proper OAuth2/OpenID Connect flow with ABP
- Use JWT tokens securely
- Store sensitive data in secure storage
- Clear auth data on logout
- Implement refresh token strategy

### Authorization
- Use route guards to protect routes
- Implement UI permission checks with directives
- Never trust client-side security alone
- Hide UI elements based on permissions
- Follow principle of least privilege


## Accessibility

### WCAG Compliance
- Follow WCAG 2.1 AA standards
- Implement proper semantic HTML
- Ensure keyboard navigation works
- Add proper ARIA labels
- Test with screen readers


## Troubleshooting and Debugging

### Common Issues
- Browser console errors
- Network request failures
- State management problems
- Memory leaks from unsubscribed observables
- Performance bottlenecks

### Debugging Tools
- Angular DevTools
- Chrome DevTools
- Augury
- Redux DevTools (for NgRx)
- Lighthouse for performance auditing