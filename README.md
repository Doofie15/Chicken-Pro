# BroilerPro - Broiler Farm Management System

A comprehensive management system for broiler farms, built with SvelteKit, Bootstrap, and Material UI. BroilerPro helps farm managers track flocks, manage customers, monitor finances, analyze performance, track feed inventory, monitor mortality, and analyze market prices in one unified platform.

## Latest Updates (April 2025)

- **Enhanced Customer Form UI**: Redesigned customer form with improved layout, document upload functionality, and Google Maps integration for address autocomplete
- **Customer Self-Registration**: Added ability to generate unique links for customers to self-register their information
- **VAT Number Field**: Added VAT number field to billing information for improved invoice processing
- **Enhanced Batch Management Form**: Streamlined form with improved supplier and weather information cards
- **Automatic Weather Data Collection**: Weather conditions are now automatically recorded upon batch creation
- **Supplier Distance Calculation**: Travel distance from suppliers is now calculated and displayed for analysis
- **Required Fields Validation**: Clear marking of mandatory fields with improved validation

![BroilerPro Dashboard](docs/screenshots/dashboard.png)

## Key Features

### Dashboard
- Visual overview of key farm metrics
- Active flocks monitoring with health status
- Recent activity tracking
- Feed inventory management
- Mortality tracking with trends
- South African market price analysis
- Financial summaries and recent transactions
- Interactive charts and graphs

### Flock Management
- Detailed view of active flocks
- Growth tracking and performance metrics
- Batch-based organization with streamlined data entry
- Quick actions for feed and mortality logging
- Mortality rate monitoring
- Feed conversion ratio analysis
- Filtering and sorting capabilities
- Automatic weather data collection for performance correlation
- Supplier distance tracking for logistics analysis

### Customer Relationship Management
- Customer profiles with comprehensive contact information
- Individual and company customer types with appropriate fields
- Google Maps integration for address autocomplete and validation
- Customer self-registration via secure unique links
- Document upload functionality for customer files
- Separate billing information with VAT number support
- Order history and preferences tracking
- Advanced filtering and search functionality

### Financial Tracking
- Revenue and expense monitoring
- Invoice management
- Financial performance visualization
- Transaction history

### Analytics
- Advanced performance insights
- Growth performance comparison
- Mortality trends analysis
- Feed conversion ratio tracking
- Weight distribution visualization

### Activity Tracking
- Recent farm activities monitoring
- User action history
- Categorized activity types
- Timestamp tracking
- Quick access to activity details

### Feed Inventory Management
- Track feed quantities by type (starter, grower, finisher)
- Monitor feed costs and usage
- Recent feed deliveries tracking
- Feed inventory visualization

### Mortality Tracking
- Monitor bird losses by batch
- Track mortality causes
- Visualize mortality trends
- Quick mortality logging

### Market Price Analysis
- Track chicken prices at major South African retailers
- Price trend monitoring
- Optimal pricing algorithm
- Revenue projection tools
- Price volatility analysis

### Settings
- Farm profile configuration
- Notification preferences
- User management and permissions
- Integration with external services
- Bootstrap 5
- Material UI (MUI for Svelte or via web components)
- Chart.js (or similar for data visualization)

## Getting Started

1. Install dependencies:
   ```
   npm install
   ```
2. Run the development server:
   ```
   npm run dev
   ```

## Structure
- `src/` - Svelte components and pages
- `src/components/` - Reusable UI components
- `src/routes/` - App routes
- `src/stores/` - State management
- `src/assets/` - Images, icons, styles

## Customization
- Easily add or rearrange dashboard widgets
- Theming via Bootstrap and MUI

---

For questions or feature requests, contact the developer.
