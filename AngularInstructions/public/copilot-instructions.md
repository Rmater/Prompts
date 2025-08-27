# Mazad Public Portal - Copilot Instructions

@include ../../../.github/shared/base-copilot-instructions.md

## Public Portal Context
This is the **customer-facing auction portal** where public users browse, register, and participate in auctions.

### Key Responsibilities
- **Auction Discovery**: Browse and search available auctions and lots
- **User Registration**: Public user account creation and profile management
- **Bidding Interface**: Real-time bidding participation and proxy bid management
- **Auction Viewing**: Live auction streaming and bid tracking
- **Payment Integration**: Post-auction payment processing and invoicing

### Technical Focus Areas
- **SEO Optimization**: Ensure auction listings are search engine friendly
- **Accessibility**: WCAG 2.1 AA compliance for inclusive user experience
- **Mobile-First Design**: Responsive interfaces optimized for mobile bidding
- **Performance**: Fast loading for auction images and real-time updates
- **Cache Strategies**: Efficient caching for auction catalogs and static content
- **CDN Integration**: Optimized delivery of auction images and videos

### Public Portal Specific Patterns
- **Anonymous User Support**: Browse auctions without authentication
- **Progressive Enhancement**: Core functionality works without JavaScript
- **Real-time Updates**: SignalR integration for live bidding
- **Image Optimization**: Lazy loading and multiple resolution support
- **Rate Limiting**: Protect against automated bidding abuse

## Coding Standards and Guidelines
@include .github/public-portal-guidelines.instructions.md

