# Customer Management Components

This document provides an overview of the customer management components in the BroilerPro application.

## CustomerForm.svelte

The main component for adding and editing customer information.

### Features

- Support for both individual and company customer types
- Google Maps integration for address autocomplete
- Document upload functionality
- Separate billing information section with VAT number support
- Form validation with error messages
- Responsive design for all device sizes

### Props

| Prop | Type | Description |
|------|------|-------------|
| customerData | Object | Customer data object with default values |

### Events

| Event | Description |
|-------|-------------|
| submit | Emitted when the form is submitted with valid data |
| cancel | Emitted when the user cancels the form |

### Usage Example

```svelte
<script>
  import CustomerForm from '$components/customers/CustomerForm.svelte';
  
  let customerData = {
    // Default values
  };
  
  function handleSubmit(event) {
    // Handle form submission
  }
  
  function handleCancel() {
    // Handle cancellation
  }
</script>

<CustomerForm 
  {customerData} 
  on:submit={handleSubmit} 
  on:cancel={handleCancel} 
/>
```

## Self-Registration Page

Located at `src/routes/customers/register/[token]/+page.svelte`, this page provides a standalone form for customers to self-register.

### Features

- Clean, standalone interface without navigation elements
- Token validation for security
- Same form fields as the main CustomerForm component
- Success message with reference number after submission
- Google Maps integration for address autocomplete

### URL Parameters

| Parameter | Description |
|-----------|-------------|
| token | A unique token for validating the registration link |

## Self-Registration Link Generator

Located in the customers list page (`src/routes/customers/+page.svelte`), this feature allows generating unique links for customer self-registration.

### Features

- Button to generate a unique registration link
- Modal dialog with copy-to-clipboard functionality
- Visual feedback when link is copied
- Secure token generation

### Implementation Details

The self-registration system works as follows:

1. Admin clicks "Self-Registration Link" button on the customers page
2. A unique token is generated and a modal displays the full registration URL
3. Admin copies the link and shares it with the customer
4. Customer opens the link in their browser
5. Token is validated on the server
6. Customer fills in their information and submits the form
7. Data is saved and a success message is displayed

## Google Maps Integration

Both the CustomerForm and self-registration page use the Google Maps JavaScript API for address autocomplete.

### Features

- Address autocomplete as the user types
- Automatic parsing of address components (street, city, postal code, etc.)
- Map preview of the selected address
- Support for both primary and billing addresses

### API Key

The Google Maps API key is stored as an environment variable and should be kept secure.
