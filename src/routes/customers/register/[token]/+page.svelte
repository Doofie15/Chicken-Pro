<script lang="ts">
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';
  
  // Get token from URL
  $: token = $page.params.token;
  
  // Form data
  let customerData = {
    id: `C-${new Date().getFullYear()}-${Math.floor(Math.random() * 1000)}`,
    type: 'individual', // individual or company
    customerType: 'Retailer', // Retailer, Wholesaler, Processor
    status: 'active',
    // Personal Information
    firstName: '',
    lastName: '',
    email: '',
    phone: '',
    idNumber: '', // ID/Passport number
    nationality: '',
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
    // Additional
    notes: '',
    image: ''
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
  
  let tokenValid = true;
  let loading = true;
  let submitting = false;
  let submitSuccess = false;
  
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
  
  // Email validation function
  function isValidEmail(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }
  
  // Validate form fields
  function validateForm() {
    let formValid = true;
    
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
  async function handleSubmit() {
    if (validateForm()) {
      submitting = true;
      
      try {
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
        
        // In a real app, this would send the data to an API
        // For now, we'll just simulate a successful submission
        console.log('Submitting customer data:', formattedData);
        await new Promise(resolve => setTimeout(resolve, 1500));
        
        submitSuccess = true;
      } catch (error) {
        console.error('Error submitting form:', error);
        alert('An error occurred while submitting the form. Please try again.');
      } finally {
        submitting = false;
      }
    }
  }
  
  // Verify token validity
  async function verifyToken() {
    loading = true;
    
    try {
      // In a real app, this would verify the token with an API
      // For now, we'll just simulate token verification
      await new Promise(resolve => setTimeout(resolve, 1000));
      
      // For demo purposes, consider all tokens valid except "invalid"
      tokenValid = token !== 'invalid';
    } catch (error) {
      console.error('Error verifying token:', error);
      tokenValid = false;
    } finally {
      loading = false;
    }
  }
  
  onMount(async () => {
    // Verify token
    verifyToken();
    
    // Initialize Google Places API
    try {
      await loadGoogleMapsAPI();
      initGooglePlaces();
      console.log('Google Maps API loaded successfully');
    } catch (error) {
      console.error('Error loading Google Maps API:', error);
    }
  });
</script>

<svelte:head>
  <title>Customer Registration | BroilerPro</title>
  <meta name="robots" content="noindex">
</svelte:head>

<div class="register-page">
  <div class="register-container">
    <div class="logo-container">
      <!-- Logo with fallback -->
      <img src="/logo.png" alt="BroilerPro Logo" class="logo-image" />
      <h1 class="logo">BroilerPro</h1>
    </div>
    
    {#if loading}
      <div class="loading-container">
        <div class="spinner"></div>
        <p>Verifying registration link...</p>
      </div>
    {:else if !tokenValid}
      <div class="error-container">
        <span class="material-icons error-icon">error</span>
        <h2>Invalid Registration Link</h2>
        <p>The registration link you are using is invalid or has expired. Please contact your supplier for a new registration link.</p>
      </div>
    {:else if submitSuccess}
      <div class="success-container">
        <span class="material-icons success-icon">check_circle</span>
        <h2>Registration Successful!</h2>
        <p>Thank you for registering. Your information has been submitted successfully.</p>
        <p>Our team will review your information and will be in touch with you shortly.</p>
        <div class="success-details">
          <p>Reference number: <strong>{customerData.id}</strong></p>
          <p>Submitted on: <strong>{new Date().toLocaleDateString()}</strong></p>
        </div>
      </div>
    {:else}
      <div class="form-container">
        <h2>Customer Registration</h2>
        <p class="form-description">Please fill in your information to complete the registration process.</p>
        
        <form on:submit|preventDefault={handleSubmit}>
          <div class="form-section">
            <h3 class="section-title">Customer Type</h3>
            
            <div class="form-group">
              <label>Customer Type</label>
              <div class="radio-group">
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
          </div>
          
          {#if customerData.type === 'individual'}
            <div class="form-section">
              <h3 class="section-title">Personal Information</h3>
              
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
                  <label for="idNumber">ID/Passport Number</label>
                  <input 
                    type="text" 
                    id="idNumber" 
                    class="form-control" 
                    bind:value={customerData.idNumber} 
                    placeholder="Enter ID or passport number"
                  />
                </div>
                
                <div class="form-group">
                  <label for="nationality">Nationality</label>
                  <select id="nationality" class="form-control" bind:value={customerData.nationality}>
                    <option value="">Select nationality</option>
                    <option value="South African">South African</option>
                    <option value="Other">Other</option>
                  </select>
                </div>
              </div>
            </div>
          {:else}
            <div class="form-section">
              <h3 class="section-title">Company Information</h3>
              
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
          {/if}
          
          <div class="form-section">
            <h3 class="section-title">Contact Information</h3>
            
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
          
          <div class="form-section">
            <h3 class="section-title">Address Information</h3>
            
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
            
            <div class="form-group map-preview">
              <label for="mapContainer">Map Preview</label>
              <div class="map-container" bind:this={mapElement}></div>
            </div>
          </div>
          
          <div class="form-section">
            <h3 class="section-title">Billing Information</h3>
            
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
                    placeholder="Enter billing email"
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
                    placeholder="Enter billing phone"
                  />
                </div>
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
          
          <div class="form-section">
            <h3 class="section-title">Additional Notes</h3>
            
            <div class="form-group">
              <textarea 
                id="notes" 
                class="form-control" 
                bind:value={customerData.notes} 
                rows="4" 
                placeholder="Enter any additional information or special requirements"
              ></textarea>
            </div>
          </div>
          
          <div class="form-actions">
            <button type="submit" class="btn btn-primary" disabled={submitting}>
              {#if submitting}
                <div class="spinner-sm"></div>
                Submitting...
              {:else}
                Complete Registration
              {/if}
            </button>
          </div>
        </form>
      </div>
    {/if}
  </div>
</div>

<style>
  .register-page {
    min-height: 100vh;
    background-color: #f8f9fa;
    padding: 40px 20px;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }
  
  .register-container {
    width: 100%;
    max-width: 800px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    overflow: hidden;
  }
  
  .success-container {
    background-color: white;
    border-radius: 8px;
    padding: 40px;
    text-align: center;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  .success-icon {
    font-size: 64px;
    color: #4caf50;
    margin-bottom: 20px;
  }
  
  .success-details {
    margin-top: 30px;
    padding-top: 20px;
    border-top: 1px solid #e0e0e0;
    text-align: left;
    font-size: 14px;
  }
  
  .success-details p {
    margin: 8px 0;
  }
  
  .loading-container, .error-container, .success-container {
    padding: 40px;
    text-align: center;
  }
  
  .spinner {
    width: 40px;
    height: 40px;
    border: 4px solid rgba(0, 0, 0, 0.1);
    border-radius: 50%;
    border-top-color: var(--primary);
    margin: 0 auto 20px;
    animation: spin 1s linear infinite;
  }
  
  .spinner-sm {
    width: 20px;
    height: 20px;
    border: 2px solid rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    border-top-color: white;
    margin-right: 10px;
    animation: spin 1s linear infinite;
    display: inline-block;
  }
  
  @keyframes spin {
    to { transform: rotate(360deg); }
  }
  
  .logo-container {
    text-align: center;
    margin-bottom: 30px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .error-icon, .success-icon {
    font-size: 64px;
    margin-bottom: 16px;
  }
  
  .error-icon {
    color: var(--danger);
  }
  
  .success-icon {
    color: var(--success);
  }
  
  .form-container {
    padding: 32px;
  }
  
  h2 {
    margin: 0 0 8px;
    font-size: 24px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .form-description {
    color: var(--gray);
    margin-bottom: 24px;
  }
  
  .form-section {
    margin-bottom: 32px;
    padding-bottom: 24px;
    border-bottom: 1px solid #f0f0f0;
  }
  
  .form-section:last-child {
    border-bottom: none;
    padding-bottom: 0;
  }
  
  .section-title {
    font-size: 18px;
    font-weight: 600;
    margin: 0 0 16px;
    color: var(--dark);
  }
  
  .form-row {
    display: flex;
    gap: 16px;
    margin-bottom: 16px;
  }
  
  @media (max-width: 768px) {
    .form-row {
      flex-direction: column;
      gap: 0;
    }
  }
  
  .form-group {
    flex: 1;
    margin-bottom: 16px;
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
  
  .radio-group, .checkbox-group {
    margin-bottom: 1rem;
  }
  
  .map-container {
    height: 300px;
    width: 100%;
    border-radius: 8px;
    overflow: hidden;
    background-color: #f5f5f5;
    margin-bottom: 20px;
  }
  
  .radio-label, .checkbox-label {
    display: flex;
    align-items: center;
    cursor: pointer;
    font-weight: normal;
  }
  
  .radio-label input, .checkbox-label input {
    margin-right: 8px;
  }
  
  textarea.form-control {
    resize: vertical;
    min-height: 100px;
  }
  
  .form-actions {
    margin-top: 32px;
    display: flex;
    justify-content: flex-end;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 10px 24px;
    border-radius: 4px;
    font-weight: 500;
    font-size: 16px;
    cursor: pointer;
    transition: all 0.2s;
    border: none;
  }
  
  .btn-primary {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-primary:hover:not(:disabled) {
    background-color: #1565c0;
  }
  
  .btn-primary:disabled {
    background-color: #90caf9;
    cursor: not-allowed;
  }
</style>
