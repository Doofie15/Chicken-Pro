# BroilerPro Component Documentation

This documentation provides detailed information about all reusable components in the BroilerPro application, their intended usage, props, and styling guidelines.

## Table of Contents

- [Core Components](#core-components)
  - [Page Layout](#page-layout)
  - [Navigation](#navigation)
  - [Forms](#forms)
- [Customer Components](#customer-components)
  - [CustomerForm](#customerform)
  - [CustomerDetails](#customerdetails)
  - [CustomerTable](#customertable)
- [Dashboard Components](#dashboard-components)
- [UI Components](#ui-components)
- [Maps Integration](#maps-integration)
- [Component Styling Guidelines](#component-styling-guidelines)

## Core Components

### Page Layout

#### `PageHeader`

Consistent header component for all pages.

**Usage:**
```svelte
<PageHeader title="Customer Details" subtitle="View and manage customer information">
  <div slot="actions">
    <button class="btn btn-primary">Add New</button>
  </div>
</PageHeader>
```

**Props:**
- `title` (string): Main page title
- `subtitle` (string, optional): Secondary descriptive text
- `slot="actions"`: Optional slot for action buttons

**Styling:**
- Uses the application's standard fonts and colors
- Displays title in 24px semibold font
- Subtitle in 14px regular font with reduced opacity
- Properly spaced action buttons

---

### Navigation

#### `TabNavigation`

Provides consistent tabbed navigation across the application.

**Usage:**
```svelte
<TabNavigation 
  tabs={[
    { id: 'overview', label: 'Overview', icon: 'dashboard' },
    { id: 'orders', label: 'Orders', icon: 'shopping_cart' },
    { id: 'financial', label: 'Financial', icon: 'account_balance' },
  ]} 
  activeTab={activeTab}
  on:tabChange={handleTabChange}
/>
```

**Props:**
- `tabs` (array): Array of tab objects with id, label, and icon
- `activeTab` (string): ID of the currently active tab
- `on:tabChange`: Event fired when a tab is clicked

**Styling:**
- Horizontal tab layout with consistent spacing
- Active tab indicators with primary color
- Material icons for visual indicators
- Responsive design for smaller screens

---

### Forms

#### `FormCard`

Wrapper component for form sections with consistent styling.

**Usage:**
```svelte
<FormCard title="Contact Information" editable={true} on:edit={toggleEdit}>
  <div class="form-row">
    <!-- Form content here -->
  </div>
</FormCard>
```

**Props:**
- `title` (string): Card title
- `editable` (boolean, optional): Whether the card has edit functionality
- `on:edit`: Event fired when edit button is clicked

**Styling:**
- 95% width, centered layout (margin: 0 auto)
- White background
- Rounded corners (border-radius: 8px or 12px)
- Subtle shadow (box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08))
- Section padding: 24px 30px
- Standard header with flex layout for title and actions

---

## Customer Components

### CustomerForm

Complete form for adding or editing customer information.

**Usage:**
```svelte
<CustomerForm 
  customer={existingCustomer} 
  on:submit={handleSubmit} 
  on:cancel={handleCancel}
/>
```

**Props:**
- `customer` (object, optional): Existing customer data for edit mode
- `on:submit`: Event with form data when submitted
- `on:cancel`: Event when cancel button is clicked

**Features:**
- Contact information section
- Business information section
- Address with Google Maps integration
- Billing information with option for same as primary address
- Document upload section
- Notes section
- Comprehensive validation

**Styling:**
- Follows FormCard styling guidelines
- Organized sections with clear visual separation
- Responsive grid layout using form-grid class

---

### CustomerDetails

Comprehensive customer details page with tabbed interface.

**Tabs:**
1. **Overview**: Contact info, business info, recent orders
2. **Orders**: Complete order history with filtering
3. **Financial**: Payment methods, credit info, invoices
4. **Delivery**: Address management with maps integration
5. **Documents**: Document management with filtering
6. **Notes & Activity**: Customer notes and interaction timeline

**Features:**
- In-place editing of all customer information
- Comprehensive filtering on all data tables
- Document upload and management
- Map integration for addresses
- Activity timeline with categorized filtering

**Styling:**
- Consistent card styling across all tabs
- Tab-based interface with smooth transitions
- Responsive layout adaptable to different screen sizes

---

### CustomerTable

Interactive table for displaying customer lists with sorting and filtering.

**Usage:**
```svelte
<CustomerTable 
  customers={customersList} 
  on:rowClick={handleCustomerSelect}
  on:action={handleAction}
/>
```

**Props:**
- `customers` (array): List of customer objects
- `on:rowClick`: Event when a customer row is clicked
- `on:action`: Event for row actions (edit, delete, etc.)

**Features:**
- Sortable columns
- Search functionality
- Status indicators
- Quick action buttons
- Pagination

**Styling:**
- Clean table design with hover effects
- Status badges with appropriate colors
- Action buttons with tooltips
- Consistent with application table styles

---

## Dashboard Components

### StatCard

Display key metric information in a consistent card format.

**Usage:**
```svelte
<StatCard 
  title="Active Flocks" 
  value="12" 
  trend="+2" 
  trendLabel="vs last month"
  icon="pets"
/>
```

**Props:**
- `title` (string): Metric name
- `value` (string/number): Primary statistic
- `trend` (string, optional): Change indicator
- `trendLabel` (string, optional): Context for trend
- `icon` (string): Material icon name

**Styling:**
- Consistent card styling (FormCard guidelines)
- Appropriate spacing for information hierarchy
- Positive/negative trend indicators with colors

---

### ChartCard

Wrapper for data visualization charts.

**Usage:**
```svelte
<ChartCard title="Growth Performance" subtitle="Last 30 days">
  <!-- Chart implementation here -->
</ChartCard>
```

**Props:**
- `title` (string): Chart title
- `subtitle` (string, optional): Additional context
- `type` (string, optional): Chart type identifier

**Styling:**
- Consistent card styling (FormCard guidelines)
- Appropriate padding for chart visualization
- Standard header with title and optional actions

---

## UI Components

### DataTable

Reusable data table component with sorting, filtering, and pagination.

**Usage:**
```svelte
<DataTable 
  data={tableData}
  columns={[
    { key: 'id', label: 'ID' },
    { key: 'name', label: 'Name' },
    { key: 'status', label: 'Status', type: 'badge' }
  ]}
  on:rowClick={handleRowClick}
/>
```

**Props:**
- `data` (array): Array of data objects
- `columns` (array): Column definitions
- `searchable` (boolean, default true): Enable search
- `paginated` (boolean, default true): Enable pagination
- `pageSize` (number, default 10): Items per page
- `on:rowClick`: Event when row is clicked

**Styling:**
- Consistent table styling with hover effects
- Search input and pagination controls
- Responsive design with horizontal scrolling on small screens

---

### StatusBadge

Display status indicators with appropriate colors.

**Usage:**
```svelte
<StatusBadge status="active" />
<StatusBadge status="completed" type="order" />
```

**Props:**
- `status` (string): Status value (active, inactive, pending, etc.)
- `type` (string, optional): Context for status (order, payment, etc.)

**Styling:**
- Consistent rounded pill design
- Color coding by status:
  - active/completed: green
  - pending/processing: blue
  - inactive/cancelled: gray
  - error/failed: red

---

### Dropdown

Consistent dropdown component for filters and selectors.

**Usage:**
```svelte
<Dropdown 
  options={statusOptions} 
  value={selectedStatus} 
  on:change={handleStatusChange}
  label="Status Filter"
/>
```

**Props:**
- `options` (array): List of options with value and label
- `value` (any): Currently selected value
- `label` (string, optional): Label for the dropdown
- `placeholder` (string, optional): Placeholder text
- `on:change`: Event when selection changes

**Styling:**
- Consistent with form controls
- Clear focus and hover states
- Appropriate dropdown menu styling

---

## Maps Integration

### AddressMap

Google Maps component for displaying and selecting addresses.

**Usage:**
```svelte
<AddressMap 
  address={customerAddress}
  editable={false}
  on:addressChange={handleAddressUpdate}
/>
```

**Props:**
- `address` (string): Address to display on map
- `editable` (boolean): Whether location can be changed
- `height` (string, default "300px"): Map container height
- `on:addressChange`: Event when address is updated (if editable)

**Features:**
- Display address with marker
- Info window with address details
- Interactive marker in edit mode
- Geocoding integration

**Integration:**
- Uses Google Maps JavaScript API
- Requires API key in environment variables
- Handles loading and error states appropriately

**Styling:**
- Consistent container with rounded corners
- Clear location marker
- Proper information display
- Loading and error states styled appropriately

---

## Component Styling Guidelines

All components in BroilerPro adhere to these styling principles:

### Form Cards

- **Width**: 95% of container (max-width: 95%)
- **Margins**: 0 auto (centered)
- **Background**: White (#fff)
- **Border Radius**: 8px or 12px
- **Shadow**: 0 4px 20px rgba(0, 0, 0, 0.08)
- **Padding**: 24px 30px for content sections

### Form Elements

- **Spacing**: 24px gap between rows
- **Input Height**: 40px for text inputs and selects
- **Input Padding**: 12px
- **Border**: 1px solid var(--gray-light)
- **Border Radius**: 4px
- **Focus State**: Border color changes to primary color

### Typography

- **Headings**:
  - h1: 24px, semi-bold
  - h2: 20px, semi-bold
  - h3: 16px, semi-bold
- **Body Text**: 14px, regular
- **Small Text**: 12px
- **Font Family**: System font stack with fallbacks

### Colors

- **Primary**: Deep blue (#1976d2)
- **Success**: Green (#4caf50)
- **Warning**: Amber (#ff9800)
- **Danger**: Red (#d32f2f)
- **Gray Scale**:
  - Light gray: #f5f5f5 (backgrounds)
  - Medium gray: #e0e0e0 (borders)
  - Dark gray: #757575 (secondary text)
- **Text**: Near black (#212121) for maximum readability

### Responsive Breakpoints

- **Mobile**: Up to 768px
- **Tablet**: 769px to 1200px
- **Desktop**: Above 1200px

Components should adapt appropriately at these breakpoints, typically switching from multi-column to single-column layouts on smaller screens.
