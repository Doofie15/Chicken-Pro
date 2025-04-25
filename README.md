# BroilerPro - Broiler Farm Management System

A comprehensive management system for broiler farms, built with SvelteKit, Bootstrap, and Material UI. BroilerPro helps farm managers track flocks, manage customers, monitor finances, analyze performance, track feed inventory, monitor mortality, and analyze market prices in one unified platform.

## Latest Updates (April 2025)

- **Enhanced Customer Details Page**: Fully implemented all tabs (Overview, Orders, Financial, Delivery, Documents, Notes & Activity) with interactive elements and modern UI
- **Interactive Maps**: Added Google Maps integration for customer addresses with markers and location information
- **Document Management System**: Implemented document upload, download, view, and delete functionality with file type filtering
- **Enhanced Filtering System**: Added comprehensive filtering capabilities across all data tables and timelines
- **Customer Timeline**: Implemented activity tracking with categorized filtering (orders, payments, deliveries, communication)
- **Delivery Management**: Added delivery address management with primary and alternative address support
- **Financial Information Management**: Enhanced credit management and payment method tracking
- **Customer Self-Registration**: Added ability to generate unique links for customers to self-register their information
- **Form Styling Consistency**: Standardized all form cards with 95% width, centered layout, white background, rounded corners, and subtle shadow effects

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
- **Enhanced Customer Details Page**: Comprehensive customer information display with tabbed interface
- **Interactive Customer Profiles**: Editable contact and business information with in-place editing
- **Google Maps Integration**: Address visualization with markers and location information
- **Document Management System**: Upload, view, download, and delete customer documents with type categorization
- **Financial Profile**: Credit limit management, preferred payment methods, and payment history tracking
- **Delivery Management**: Multiple address support with primary/alternative designation and map visualization
- **Activity Timeline**: Comprehensive customer interaction history with activity filtering
- **Notes System**: Editable customer notes for important information tracking
- **Order Management**: Complete order history with filtering by status and date range
- **Customer Self-Registration**: Secure registration links for customer onboarding
- **Advanced Filtering**: Comprehensive filtering across all data tables and timelines

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

## Technology Stack

- **Frontend**: SvelteKit, TypeScript
- **UI Framework**: Custom CSS with design system
- **Mapping**: Google Maps JavaScript API
- **Icons**: Material Icons
- **Data Visualization**: Chart.js

## Getting Started

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/broiler-farm-app.git
   cd broiler-farm-app
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Set up environment variables:
   Create a `.env` file in the project root with the following variables:
   ```
   GOOGLE_MAPS_API_KEY=your_google_maps_api_key
   ```

4. Start the development server:
   ```bash
   npm run dev
   ```

5. Build for production:
   ```bash
   npm run build
   ```

## Project Structure

```
├── src/
│   ├── components/           # Reusable UI components
│   │   ├── customers/        # Customer-related components
│   │   ├── dashboard/        # Dashboard-related components
│   │   └── ui/               # Generic UI components
│   ├── routes/               # SvelteKit routes
│   │   ├── customers/        # Customer-related pages
│   │   ├── dashboard/        # Dashboard pages
│   │   ├── flocks/           # Flock management
│   │   └── settings/         # Application settings
│   ├── lib/                  # Shared utilities and functions
│   └── app.html              # Main HTML template
├── static/                   # Static assets
├── package.json              # Project dependencies
└── svelte.config.js          # SvelteKit configuration
```

## Design System

All form cards in the application follow these styling guidelines:

- **Width**: 95% of the page width (max-width: 95%)
- **Centered Layout**: margin: 0 auto
- **Background**: White (#fff)
- **Corners**: Rounded (border-radius: 8px or 12px)
- **Shadow**: Subtle shadow effect (box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08))
- **Padding**: 24px 30px for sections
- **Spacing**: 24px gap between form elements
- **Form Groups**: flex basis of 350px

## Component Documentation

See the [COMPONENTS.md](docs/COMPONENTS.md) file for detailed documentation on all reusable components in the application.

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

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
