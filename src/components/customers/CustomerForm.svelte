<script lang="ts">
  import { createEventDispatcher, onMount } from 'svelte';
  
  const dispatch = createEventDispatcher();
  
  // Current date in YYYY-MM-DD format for default values
  const today = new Date().toISOString().split('T')[0];
  
  // Define document type
  interface Document {
    id: string;
    name: string;
    type: string;
    size: number;
    data: string;
    uploadDate: string;
  }
  
  // Default values for the form
  export let customerData = {
    id: `C-${new Date().getFullYear()}-${Math.floor(Math.random() * 1000)}`,
    type: 'individual', // individual or company
    customerType: 'Retailer', // Retailer, Wholesaler, Processor
    status: 'active',
    // Personal Information
    firstName: '',  
    lastName: '',
    email: '',
    phone: '',
    // Company Information (if type is company)
    companyName: '',
    contactPerson: '',
    taxId: '',
    // Address Information
    addressLine1: '',
    addressLine2: '',
    city: '',
    postalCode: '',
    country: '',
    state: '',
    coordinates: { lat: 0, lng: 0 },
    // Billing Information
    sameAsPrimary: true, // Whether billing address is same as primary address
    billingAddressLine1: '',
    billingAddressLine2: '',
    billingCity: '',
    billingPostalCode: '',
    billingCountry: '',
    billingState: '',
    billingEmail: '',
    billingPhone: '',
    billingContactPerson: '',
    billingVatNumber: '',
    // Additional
    notes: '',
    documents: [] as Document[]
  };
  
  // Form validation
  let errors: Record<string, string> = {
    firstName: '',
    lastName: '',
    email: '',
    phone: '',
    companyName: '',
    contactPerson: '',
    addressLine1: '',
    city: '',
    postalCode: '',
    country: '',
    billingAddressLine1: '',
    billingCity: '',
    billingPostalCode: '',
    billingCountry: '',
    billingEmail: ''
  };
  let formValid = true;
  
  // Customer types
  const customerTypes = [
    'Retailer',
    'Wholesaler',
    'Processor',
    'Individual',
    'Other'
  ];
  
  // Initialize form data and state variables
  let showBillingSection = false;
  let documentFileInput: HTMLInputElement;
  
  // Document file input reference
  function triggerDocumentFileInput() {
    documentFileInput.click();
  }
  
  // Handle key press for accessibility
  function handleKeyDown(event: KeyboardEvent) {
    if (event.key === 'Enter' || event.key === ' ') {
      event.preventDefault();
      triggerDocumentFileInput();
    }
  }
  
  // Handle document file change
  function handleDocumentFileChange(event: Event) {
    const target = event.target as HTMLInputElement;
    if (target.files && target.files[0]) {
      const file = target.files[0];
      const reader = new FileReader();
      
      reader.onload = (e) => {
        // Add document to the list
        customerData.documents = [
          ...customerData.documents,
          {
            id: `doc-${Date.now()}`,
            name: file.name,
            type: file.type,
            size: file.size,
            data: e.target?.result as string,
            uploadDate: new Date().toISOString()
          }
        ];
      };
      
      reader.readAsDataURL(file);
    }
  }
  
  // Remove document
  function removeDocument(docId: string) {
    customerData.documents = customerData.documents.filter(doc => doc.id !== docId);
  }
  
  // Format file size
  function formatFileSize(bytes: number): string {
    if (bytes < 1024) return bytes + ' bytes';
    if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(1) + ' KB';
    return (bytes / (1024 * 1024)).toFixed(1) + ' MB';
  }
  
  // Update billing address when sameAsPrimary changes
  function updateBillingAddress() {
    if (customerData.sameAsPrimary) {
      customerData.billingAddressLine1 = customerData.addressLine1;
      customerData.billingAddressLine2 = customerData.addressLine2;
      customerData.billingCity = customerData.city;
      customerData.billingPostalCode = customerData.postalCode;
      customerData.billingCountry = customerData.country;
      customerData.billingState = customerData.state;
      customerData.billingEmail = customerData.email;
      customerData.billingPhone = customerData.phone;
      customerData.billingContactPerson = customerData.type === 'company' ? customerData.contactPerson : '';
    }
  }
  
  // Watch for changes to primary address and update billing if needed
  $: if (customerData.sameAsPrimary) {
    updateBillingAddress();
  }
  
  // Variables for Google Places autocomplete
  let addressInput: HTMLInputElement;
  let billingAddressInput: HTMLInputElement;
  
  // Define Google Maps types
  interface GooglePlace {
    formatted_address?: string;
    address_components?: Array<{
      long_name: string;
      short_name: string;
      types: string[];
    }>;
    geometry?: {
      location: {
        lat: () => number;
        lng: () => number;
      }
    };
  }
  
  // Map instance
  let map: any;
  let mapElement: HTMLDivElement;
  
  // Function to parse address components from Google Places
  function parseAddressComponents(place: GooglePlace, isBilling = false) {
    if (!place.address_components) return;
    
    // Extract address components
    let streetNumber = '';
    let route = '';
    let city = '';
    let state = '';
    let postalCode = '';
    let country = '';
    
    place.address_components.forEach(component => {
      const types = component.types;
      
      if (types.includes('street_number')) {
        streetNumber = component.long_name;
      } else if (types.includes('route')) {
        route = component.long_name;
      } else if (types.includes('locality') || types.includes('sublocality')) {
        city = component.long_name;
      } else if (types.includes('administrative_area_level_1')) {
        state = component.long_name;
      } else if (types.includes('postal_code')) {
        postalCode = component.long_name;
      } else if (types.includes('country')) {
        country = component.long_name;
      }
    });
    
    // Create address line 1 from street number and route
    const addressLine1 = streetNumber && route ? `${streetNumber} ${route}` : place.formatted_address || '';
    
    // Update the appropriate fields
    if (isBilling) {
      customerData.billingAddressLine1 = addressLine1;
      customerData.billingCity = city;
      customerData.billingState = state;
      customerData.billingPostalCode = postalCode;
      customerData.billingCountry = country;
    } else {
      customerData.addressLine1 = addressLine1;
      customerData.city = city;
      customerData.state = state;
      customerData.postalCode = postalCode;
      customerData.country = country;
      
      // Update coordinates
      if (place.geometry && place.geometry.location) {
        customerData.coordinates = {
          lat: place.geometry.location.lat(),
          lng: place.geometry.location.lng()
        };
        
        // Initialize or update map
        initMap();
      }
    }
  }
  
  // Initialize or update map
  function initMap() {
    if (typeof window !== 'undefined' && 'google' in window && mapElement) {
      const google = (window as any).google;
      
      if (customerData.coordinates.lat && customerData.coordinates.lng) {
        const position = {
          lat: customerData.coordinates.lat,
          lng: customerData.coordinates.lng
        };
        
        if (!map) {
          // Create new map
          map = new google.maps.Map(mapElement, {
            center: position,
            zoom: 15,
            mapTypeControl: false,
            streetViewControl: false
          });
        } else {
          // Update existing map
          map.setCenter(position);
        }
        
        // Add marker
        new google.maps.Marker({
          position,
          map,
          title: customerData.type === 'individual' 
            ? `${customerData.firstName} ${customerData.lastName}` 
            : customerData.companyName
        });
      }
    }
  }
  
  // Function to initialize Google Places autocomplete
  function initGooglePlaces() {
    // Check if google is defined in the global scope
    if (typeof window !== 'undefined' && 'google' in window) {
      const google = (window as any).google;
      
      if (google?.maps?.places && addressInput) {
        // Initialize autocomplete for primary address
        const addressAutocomplete = new google.maps.places.Autocomplete(addressInput);
        addressAutocomplete.addListener('place_changed', () => {
          const place = addressAutocomplete.getPlace() as GooglePlace;
          if (place.geometry) {
            parseAddressComponents(place);
            
            // Update billing if same as primary
            if (customerData.sameAsPrimary) {
              updateBillingAddress();
            }
          }
        });
      }
      
      if (google?.maps?.places && billingAddressInput) {
        // Initialize autocomplete for billing address
        const billingAutocomplete = new google.maps.places.Autocomplete(billingAddressInput);
        billingAutocomplete.addListener('place_changed', () => {
          const place = billingAutocomplete.getPlace() as GooglePlace;
          if (place.geometry) {
            parseAddressComponents(place, true);
          }
        });
      }
    }
  }
  
  // Validate form fields
  function validateForm() {
    formValid = true;
    
    // Reset errors
    Object.keys(errors).forEach(key => {
      errors[key] = '';
    });
    
    if (customerData.type === 'individual') {
      // Validate individual customer fields
      if (!customerData.firstName.trim()) {
        errors.firstName = 'First name is required';
        formValid = false;
      }
      
      if (!customerData.lastName.trim()) {
        errors.lastName = 'Last name is required';
        formValid = false;
      }
    } else {
      // Validate company fields
      if (!customerData.companyName.trim()) {
        errors.companyName = 'Company name is required';
        formValid = false;
      }
      
      if (!customerData.contactPerson.trim()) {
        errors.contactPerson = 'Contact person is required';
        formValid = false;
      }
    }
    
    // Validate email
    if (!customerData.email.trim()) {
      errors.email = 'Email is required';
      formValid = false;
    } else if (!isValidEmail(customerData.email)) {
      errors.email = 'Please enter a valid email address';
      formValid = false;
    }
    
    // Validate phone
    if (!customerData.phone.trim()) {
      errors.phone = 'Phone number is required';
      formValid = false;
    }
    
    // Validate address
    if (!customerData.addressLine1.trim()) {
      errors.addressLine1 = 'Address line 1 is required';
      formValid = false;
    }
    
    if (!customerData.city.trim()) {
      errors.city = 'City is required';
      formValid = false;
    }
    
    if (!customerData.postalCode.trim()) {
      errors.postalCode = 'Postal code is required';
      formValid = false;
    }
    
    if (!customerData.country.trim()) {
      errors.country = 'Country is required';
      formValid = false;
    }
    
    // Validate billing information if not same as primary
    if (!customerData.sameAsPrimary) {
      if (!customerData.billingAddressLine1.trim()) {
        errors.billingAddressLine1 = 'Billing address is required';
        formValid = false;
      }
      
      if (!customerData.billingCity.trim()) {
        errors.billingCity = 'Billing city is required';
        formValid = false;
      }
      
      if (!customerData.billingPostalCode.trim()) {
        errors.billingPostalCode = 'Billing postal code is required';
        formValid = false;
      }
      
      if (!customerData.billingCountry.trim()) {
        errors.billingCountry = 'Billing country is required';
        formValid = false;
      }
      
      if (!customerData.billingEmail.trim()) {
        errors.billingEmail = 'Billing email is required';
        formValid = false;
      } else if (!isValidEmail(customerData.billingEmail)) {
        errors.billingEmail = 'Please enter a valid billing email address';
        formValid = false;
      }
    }
    
    return formValid;
  }
  
  // Handle form submission
  function handleSubmit() {
    if (validateForm()) {
      // Format the data for submission
      const formattedData = {
        ...customerData,
        // Create a full name from first and last name for individuals
        ...(customerData.type === 'individual' && {
          fullName: `${customerData.firstName} ${customerData.lastName}`.trim()
        }),
        // Use company name as the full name for companies
        ...(customerData.type === 'company' && {
          fullName: customerData.companyName
        })
      };
      
      // If billing address is same as primary, copy the primary address details
      if (formattedData.sameAsPrimary) {
        formattedData.billingAddressLine1 = formattedData.addressLine1;
        formattedData.billingAddressLine2 = formattedData.addressLine2;
        formattedData.billingCity = formattedData.city;
        formattedData.billingPostalCode = formattedData.postalCode;
        formattedData.billingCountry = formattedData.country;
        formattedData.billingState = formattedData.state;
        formattedData.billingEmail = formattedData.email;
        formattedData.billingPhone = formattedData.phone;
      }
      
      // Dispatch the submit event with the formatted data
      dispatch('submit', formattedData);
    }
  }
  
  // Email validation function
  function isValidEmail(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }
  
  // Handle form cancellation
  function handleCancel(): void {
    dispatch('cancel');
  }
  
  // Load Google Maps API script
  function loadGoogleMapsAPI() {
    return new Promise<void>((resolve, reject) => {
      // Check if the API is already loaded
      if (typeof window !== 'undefined' && 'google' in window) {
        resolve();
        return;
      }

      // Create script element
      const script = document.createElement('script');
      const apiKey = 'AIzaSyD3wWF2a8TwWnXG7W_8ALtydF1si4JCpOY';
      script.src = `https://maps.googleapis.com/maps/api/js?key=${apiKey}&libraries=places`;
      script.async = true;
      script.defer = true;
      
      // Set up callbacks
      script.onload = () => resolve();
      script.onerror = (error) => reject(new Error(`Google Maps API failed to load: ${error}`));
      
      // Add to document
      document.head.appendChild(script);
    });
  }
  
  // Initialize Google Places API when component mounts
  onMount(async () => {
    try {
      await loadGoogleMapsAPI();
      initGooglePlaces();
      console.log('Google Maps API loaded successfully');
    } catch (error) {
      console.error('Error loading Google Maps API:', error);
    }
  });
</script>

<div class="customer-form-container">
  <form on:submit|preventDefault={handleSubmit} class="customer-form">
    <div class="form-container">
      
      <!-- Customer Type Selection -->
      <div class="form-section">
        <div class="section-header">
          <div class="section-icon">
            <span class="material-icons">category</span>
          </div>
          <div>
            <h3 class="section-title">Customer Type</h3>
            <p class="section-description">Select the type of customer</p>
          </div>
        </div>
        <div class="section-content">
          <div class="form-row">
            <div class="form-group">
              <label id="customer-type-label" for="customer-type-group">Customer Type</label>
              <div class="radio-group" id="customer-type-group" aria-labelledby="customer-type-label">
                <label class="radio-label">
                  <input type="radio" name="type" value="individual" bind:group={customerData.type}>
                  <span>Individual</span>
                </label>
                <label class="radio-label">
                  <input type="radio" name="type" value="company" bind:group={customerData.type}>
                  <span>Company</span>
                </label>
              </div>
            </div>
            
            <div class="form-group">
              <label id="customer-category-label" for="customer-category-group">Customer Category</label>
              <select id="customer-category-group" class="form-control" bind:value={customerData.customerType}>
                {#each customerTypes as type}
                  <option value={type}>{type}</option>
                {/each}
              </select>
            </div>
            
            <div class="form-group">
              <label for="customerId">Customer ID</label>
              <input 
                type="text" 
                id="customerId" 
                class="form-control" 
                bind:value={customerData.id} 
                readonly
              />
              <small class="form-text">Auto-generated ID</small>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Personal Information -->
      {#if customerData.type === 'individual'}
        <div class="form-section">
          <div class="section-header">
            <div class="section-icon">
              <span class="material-icons">person</span>
            </div>
            <div>
              <h3 class="section-title">Personal Information</h3>
              <p class="section-description">Enter the individual's personal details</p>
            </div>
          </div>
          <div class="section-content">
            <div class="form-row">
              <div class="form-group">
                <label for="firstName">First Name <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="firstName" 
                  class="form-control {errors.firstName ? 'is-invalid' : ''}" 
                  bind:value={customerData.firstName} 
                  placeholder="Enter first name"
                />
                {#if errors.firstName}
                  <div class="invalid-feedback">{errors.firstName}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="lastName">Last Name <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="lastName" 
                  class="form-control {errors.lastName ? 'is-invalid' : ''}" 
                  bind:value={customerData.lastName} 
                  placeholder="Enter last name"
                />
                {#if errors.lastName}
                  <div class="invalid-feedback">{errors.lastName}</div>
                {/if}
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="customerEmail">Email <span class="required">*</span></label>
                <input 
                  type="email" 
                  id="customerEmail" 
                  class="form-control {errors.email ? 'is-invalid' : ''}" 
                  bind:value={customerData.email} 
                  placeholder="Enter email address"
                />
                {#if errors.email}
                  <div class="invalid-feedback">{errors.email}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="customerPhone">Phone (WhatsApp) <span class="required">*</span></label>
                <input 
                  type="tel" 
                  id="customerPhone" 
                  class="form-control {errors.phone ? 'is-invalid' : ''}" 
                  bind:value={customerData.phone} 
                  placeholder="Enter phone number"
                />
                {#if errors.phone}
                  <div class="invalid-feedback">{errors.phone}</div>
                {/if}
              </div>
            </div>
            

          </div>
        </div>
      {:else}
        <!-- Company Information -->
        <div class="form-section">
          <div class="section-header">
            <div class="section-icon">
              <span class="material-icons">business</span>
            </div>
            <div>
              <h3 class="section-title">Company Information</h3>
              <p class="section-description">Enter the company details</p>
            </div>
          </div>
          <div class="section-content">
            <div class="form-row">
              <div class="form-group">
                <label for="companyName">Company Name <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="companyName" 
                  class="form-control {errors.companyName ? 'is-invalid' : ''}" 
                  bind:value={customerData.companyName} 
                  placeholder="Enter company name"
                />
                {#if errors.companyName}
                  <div class="invalid-feedback">{errors.companyName}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="taxId">Tax ID / VAT Number</label>
                <input 
                  type="text" 
                  id="taxId" 
                  class="form-control" 
                  bind:value={customerData.taxId} 
                  placeholder="Enter tax ID or VAT number"
                />
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="contactPerson">Contact Person <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="contactPerson" 
                  class="form-control {errors.contactPerson ? 'is-invalid' : ''}" 
                  bind:value={customerData.contactPerson} 
                  placeholder="Enter primary contact name"
                />
                {#if errors.contactPerson}
                  <div class="invalid-feedback">{errors.contactPerson}</div>
                {/if}
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="customerEmail">Email <span class="required">*</span></label>
                <input 
                  type="email" 
                  id="customerEmail" 
                  class="form-control {errors.email ? 'is-invalid' : ''}" 
                  bind:value={customerData.email} 
                  placeholder="Enter email address"
                />
                {#if errors.email}
                  <div class="invalid-feedback">{errors.email}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="customerPhone">Phone <span class="required">*</span></label>
                <input 
                  type="tel" 
                  id="customerPhone" 
                  class="form-control {errors.phone ? 'is-invalid' : ''}" 
                  bind:value={customerData.phone} 
                  placeholder="Enter phone number"
                />
                {#if errors.phone}
                  <div class="invalid-feedback">{errors.phone}</div>
                {/if}
              </div>
            </div>
          </div>
        </div>
      {/if}
      
      <!-- Address Information -->
      <div class="form-section">
        <div class="section-header">
          <div class="section-icon">
            <span class="material-icons">location_on</span>
          </div>
          <div>
            <h3 class="section-title">Address Information</h3>
            <p class="section-description">Enter the customer's address details</p>
          </div>
        </div>
        <div class="section-content">
          <div class="form-group">
            <label for="customerAddress">Search Address</label>
            <input 
              type="text" 
              id="customerAddress" 
              class="form-control" 
              placeholder="Search for an address..." 
              bind:this={addressInput}
            />
            <small class="form-text">Search for your address using Google Maps</small>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label for="addressLine1">Address Line 1 <span class="required">*</span></label>
              <input 
                type="text" 
                id="addressLine1" 
                class="form-control {errors.addressLine1 ? 'is-invalid' : ''}" 
                bind:value={customerData.addressLine1} 
                placeholder="Enter address line 1"
              />
              {#if errors.addressLine1}
                <div class="invalid-feedback">{errors.addressLine1}</div>
              {/if}
            </div>
            
            <div class="form-group">
              <label for="addressLine2">Address Line 2</label>
              <input 
                type="text" 
                id="addressLine2" 
                class="form-control" 
                bind:value={customerData.addressLine2} 
                placeholder="Enter address line 2 (optional)"
              />
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label for="city">City <span class="required">*</span></label>
              <input 
                type="text" 
                id="city" 
                class="form-control {errors.city ? 'is-invalid' : ''}" 
                bind:value={customerData.city} 
                placeholder="Enter city"
              />
              {#if errors.city}
                <div class="invalid-feedback">{errors.city}</div>
              {/if}
            </div>
            
            <div class="form-group">
              <label for="postalCode">Postal Code <span class="required">*</span></label>
              <input 
                type="text" 
                id="postalCode" 
                class="form-control {errors.postalCode ? 'is-invalid' : ''}" 
                bind:value={customerData.postalCode} 
                placeholder="Enter postal code"
              />
              {#if errors.postalCode}
                <div class="invalid-feedback">{errors.postalCode}</div>
              {/if}
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label for="country">Country <span class="required">*</span></label>
              <select 
                id="country" 
                class="form-control {errors.country ? 'is-invalid' : ''}" 
                bind:value={customerData.country}
              >
                <option value="">Select country</option>
                <option value="South Africa">South Africa</option>
                <option value="Other">Other</option>
              </select>
              {#if errors.country}
                <div class="invalid-feedback">{errors.country}</div>
              {/if}
            </div>
            
            <div class="form-group">
              <label for="state">State/Province</label>
              <input 
                type="text" 
                id="state" 
                class="form-control" 
                bind:value={customerData.state} 
                placeholder="Enter state or province"
              />
            </div>
          </div>
          
          <!-- Map Preview -->
          <div class="form-group map-preview">
            <label id="mapPreviewLabel">Map Preview</label>
            <div class="map-container" bind:this={mapElement} aria-labelledby="mapPreviewLabel"></div>
          </div>
        </div>
      </div>
      
      <!-- Billing Information -->
      <div class="form-section">
        <div class="section-header">
          <div class="section-icon">
            <span class="material-icons">receipt</span>
          </div>
          <div>
            <h3 class="section-title">Billing Information</h3>
            <p class="section-description">Enter the customer's billing information</p>
          </div>
        </div>
        <div class="section-content">
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" bind:checked={customerData.sameAsPrimary} on:change={updateBillingAddress}>
              <span>Same as primary address</span>
            </label>
          </div>
          
          {#if !customerData.sameAsPrimary}
            <div class="form-group">
              <label for="billingAddressSearch">Search Billing Address</label>
              <input 
                type="text" 
                id="billingAddressSearch" 
                class="form-control" 
                placeholder="Search for a billing address..." 
                bind:this={billingAddressInput}
              />
              <small class="form-text">Search for your billing address using Google Maps</small>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="billingAddressLine1">Billing Address Line 1 <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="billingAddressLine1" 
                  class="form-control {errors.billingAddressLine1 ? 'is-invalid' : ''}" 
                  bind:value={customerData.billingAddressLine1} 
                  placeholder="Enter billing address line 1"
                />
                {#if errors.billingAddressLine1}
                  <div class="invalid-feedback">{errors.billingAddressLine1}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="billingAddressLine2">Billing Address Line 2</label>
                <input 
                  type="text" 
                  id="billingAddressLine2" 
                  class="form-control" 
                  bind:value={customerData.billingAddressLine2} 
                  placeholder="Enter billing address line 2 (optional)"
                />
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="billingCity">Billing City <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="billingCity" 
                  class="form-control {errors.billingCity ? 'is-invalid' : ''}" 
                  bind:value={customerData.billingCity} 
                  placeholder="Enter billing city"
                />
                {#if errors.billingCity}
                  <div class="invalid-feedback">{errors.billingCity}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="billingPostalCode">Billing Postal Code <span class="required">*</span></label>
                <input 
                  type="text" 
                  id="billingPostalCode" 
                  class="form-control {errors.billingPostalCode ? 'is-invalid' : ''}" 
                  bind:value={customerData.billingPostalCode} 
                  placeholder="Enter billing postal code"
                />
                {#if errors.billingPostalCode}
                  <div class="invalid-feedback">{errors.billingPostalCode}</div>
                {/if}
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="billingCountry">Billing Country <span class="required">*</span></label>
                <select 
                  id="billingCountry" 
                  class="form-control {errors.billingCountry ? 'is-invalid' : ''}" 
                  bind:value={customerData.billingCountry}
                >
                  <option value="">Select country</option>
                  <option value="South Africa">South Africa</option>
                  <option value="Other">Other</option>
                </select>
                {#if errors.billingCountry}
                  <div class="invalid-feedback">{errors.billingCountry}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="billingState">Billing State/Province</label>
                <input 
                  type="text" 
                  id="billingState" 
                  class="form-control" 
                  bind:value={customerData.billingState} 
                  placeholder="Enter billing state or province"
                />
              </div>
            </div>
            
            <div class="form-row">
              <div class="form-group">
                <label for="billingEmail">Billing Email <span class="required">*</span></label>
                <input 
                  type="email" 
                  id="billingEmail" 
                  class="form-control {errors.billingEmail ? 'is-invalid' : ''}" 
                  bind:value={customerData.billingEmail} 
                  placeholder="Enter billing email address"
                />
                {#if errors.billingEmail}
                  <div class="invalid-feedback">{errors.billingEmail}</div>
                {/if}
              </div>
              
              <div class="form-group">
                <label for="billingPhone">Billing Phone</label>
                <input 
                  type="tel" 
                  id="billingPhone" 
                  class="form-control" 
                  bind:value={customerData.billingPhone} 
                  placeholder="Enter billing phone number"
                />
              </div>
            </div>
            
            <div class="form-group">
              <label for="billingVatNumber">VAT Number</label>
              <input 
                type="text" 
                id="billingVatNumber" 
                class="form-control" 
                bind:value={customerData.billingVatNumber} 
                placeholder="Enter VAT number"
              />
            </div>
            
            {#if customerData.type === 'company'}
              <div class="form-group">
                <label for="billingContactPerson">Billing Contact Person</label>
                <input 
                  type="text" 
                  id="billingContactPerson" 
                  class="form-control" 
                  bind:value={customerData.billingContactPerson} 
                  placeholder="Enter billing contact person"
                />
              </div>
            {/if}
          {/if}
        </div>
      </div>
      
      <!-- Document Upload -->
      <div class="form-section">
        <div class="section-header">
          <div class="section-icon">
            <span class="material-icons">description</span>
          </div>
          <div>
            <h3 class="section-title">Customer Documents</h3>
            <p class="section-description">Upload and manage customer documents</p>
          </div>
        </div>
        <div class="section-content">
          <div class="form-group">
            <button type="button" class="btn btn-outline-primary" on:click={triggerDocumentFileInput}>
              <i class="material-icons">upload_file</i>
              Upload Document
            </button>
            <input 
              id="documentFileUpload"
              type="file" 
              accept=".pdf,.doc,.docx,.jpg,.jpeg,.png" 
              style="display: none;" 
              bind:this={documentFileInput} 
              on:change={handleDocumentFileChange}
            />
          </div>
          
          {#if customerData.documents && customerData.documents.length > 0}
            <div class="documents-list">
              <h4>Uploaded Documents</h4>
              <div class="document-items">
                {#each customerData.documents as document (document.id)}
                  <div class="document-item">
                    <div class="document-icon">
                      <i class="material-icons">
                        {document.type.includes('pdf') ? 'picture_as_pdf' : 
                         document.type.includes('doc') ? 'article' : 
                         document.type.includes('image') ? 'image' : 'insert_drive_file'}
                      </i>
                    </div>
                    <div class="document-info">
                      <div class="document-name">{document.name}</div>
                      <div class="document-meta">
                        <span>{new Date(document.uploadDate).toLocaleDateString()}</span>
                        <span>{formatFileSize(document.size)}</span>
                      </div>
                    </div>
                    <div class="document-actions">
                      <button type="button" class="btn btn-sm btn-outline-danger" on:click={() => removeDocument(document.id)}>
                        <i class="material-icons">delete</i>
                      </button>
                    </div>
                  </div>
                {/each}
              </div>
            </div>
          {:else}
            <p class="no-documents">No documents uploaded yet.</p>
          {/if}
        </div>
      </div>
      
      <!-- Additional Notes -->
      <div class="form-section">
        <div class="section-header">
          <div class="section-icon">
            <span class="material-icons">notes</span>
          </div>
          <div>
            <h3 class="section-title">Additional Notes</h3>
            <p class="section-description">Add any additional information about this customer</p>
          </div>
        </div>
        <div class="section-content">
        
        <div class="form-group">
          <textarea 
            id="notes" 
            class="form-control" 
            bind:value={customerData.notes} 
            rows="4" 
            placeholder="Enter any additional notes or information about this customer"
          ></textarea>
        </div>
        </div>
      </div>
    </div>
    
    <div class="form-actions">
      <button type="button" class="btn btn-outline-secondary" on:click={handleCancel}>
        Cancel
      </button>
      <button type="submit" class="btn btn-primary">
        <span class="material-icons">save</span>
        Save Customer
      </button>
    </div>
  </form>
</div>

<style>
  .customer-form-container {
    max-width: 95%;
    margin: 0 auto;
    padding: 20px;
  }
  
  .form-container {
    background-color: #fff;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    overflow: hidden;
    margin-bottom: 24px;
  }
  
  .form-section {
    padding: 0;
    margin-bottom: 0;
    border-bottom: 1px solid #f0f0f0;
  }
  
  .form-section:last-child {
    border-bottom: none;
  }
  
  .section-header {
    display: flex;
    align-items: center;
    padding: 20px 24px;
    background-color: #f8f9fa;
    cursor: pointer;
    transition: background-color 0.2s;
  }
  
  .section-header:hover {
    background-color: #f1f3f5;
  }
  
  .section-icon {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background-color: var(--primary);
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .section-icon .material-icons {
    font-size: 20px;
  }
  
  .section-title {
    font-size: 18px;
    font-weight: 600;
    margin: 0 0 4px;
    color: var(--dark);
  }
  
  .section-description {
    font-size: 14px;
    color: var(--gray);
    margin: 0;
  }
  
  .section-content {
    padding: 24px 30px;
  }
  
  .form-row {
    display: flex;
    flex-wrap: wrap;
    gap: 24px;
    margin-bottom: 20px;
  }
  
  .form-group {
    flex: 1 1 350px;
    margin-bottom: 20px;
  }
  
  .map-container {
    height: 300px;
    width: 100%;
    border-radius: 8px;
    overflow: hidden;
    background-color: #f5f5f5;
    margin-bottom: 20px;
  }
  
  .map-preview {
    margin-top: 15px;
  }
  
  label {
    display: block;
    margin-bottom: 8px;
    font-weight: 500;
    font-size: 14px;
    color: var(--dark);
  }
  
  .form-control {
    width: 100%;
    padding: 10px 12px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 14px;
    transition: border-color 0.2s;
  }
  
  .form-control:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 3px rgba(25, 118, 210, 0.1);
  }
  
  .form-control.is-invalid {
    border-color: var(--danger);
  }
  
  .invalid-feedback {
    color: var(--danger);
    font-size: 12px;
    margin-top: 4px;
  }
  
  .form-text {
    font-size: 12px;
    color: var(--gray);
    margin-top: 4px;
  }
  
  .radio-group {
    display: flex;
    gap: 16px;
  }
  
  .radio-label {
    display: flex;
    align-items: center;
    cursor: pointer;
    font-weight: normal;
  }
  
  .radio-label input {
    margin-right: 8px;
  }
  
  .documents-list {
    margin-top: 20px;
  }
  
  .documents-list h4 {
    font-size: 16px;
    margin-bottom: 15px;
    color: #555;
  }
  
  .document-items {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  
  .document-item {
    display: flex;
    align-items: center;
    padding: 12px;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    background-color: #f9f9f9;
    transition: all 0.2s ease;
  }
  
  .document-item:hover {
    background-color: #f0f0f0;
    border-color: #d0d0d0;
  }
  
  .document-icon {
    margin-right: 12px;
  }
  
  .document-icon i {
    font-size: 24px;
    color: #555;
  }
  
  .document-info {
    flex: 1;
  }
  
  .document-name {
    font-weight: 500;
    margin-bottom: 4px;
    color: #333;
  }
  
  .document-meta {
    display: flex;
    gap: 15px;
    font-size: 12px;
    color: #777;
  }
  
  .document-actions {
    display: flex;
    gap: 5px;
  }
  
  .no-documents {
    color: #777;
    font-style: italic;
    margin-top: 10px;
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 16px;
    margin-top: 24px;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    padding: 10px 16px;
    border-radius: 4px;
    font-weight: 500;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
    border: none;
  }
  
  .btn .material-icons {
    font-size: 18px;
    margin-right: 8px;
  }
  
  .btn-primary {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-primary:hover {
    background-color: #1565c0;
  }
  
  .btn-outline-primary {
    background-color: transparent;
    border: 1px solid var(--primary);
    color: var(--primary);
  }
  
  .btn-outline-primary:hover {
    background-color: rgba(25, 118, 210, 0.1);
  }
  
  .btn-outline-secondary {
    background-color: transparent;
    border: 1px solid #ddd;
    color: var(--gray);
  }
  
  .btn-outline-secondary:hover {
    background-color: #f5f5f5;
  }
  
  textarea.form-control {
    resize: vertical;
    min-height: 100px;
  }
</style>
