<script>
  import { createEventDispatcher } from 'svelte';
  import { onMount } from 'svelte';
  
  const dispatch = createEventDispatcher();
  
  // Current date in YYYY-MM-DD format for default values
  const today = new Date().toISOString().split('T')[0];
  const nextYear = new Date();
  nextYear.setDate(nextYear.getDate() + 42); // Default cycle of 42 days
  const projectedEndDate = nextYear.toISOString().split('T')[0];
  
  // Get next batch number - auto-incrementing
  // In a real app, this would be fetched from the API
  let lastBatchNumber = 45; // This would come from the API
  let nextBatchNumber = lastBatchNumber + 1;
  
  // Default values for the form
  export let batchData = {
    id: `B-${new Date().getFullYear()}-${nextBatchNumber}`,
    name: `Batch ${nextBatchNumber}`,
    startDate: today,
    projectedEndDate: projectedEndDate,
    initialCount: '',
    deadOnArrival: '',
    cycleDays: 42,
    breedType: 'Ross 308',
    farmSection: 'Section A',
    sourceHatchery: 'National Chicks',
    feedType: 'Standard',
    currentWeight: '', // Initial weight of day-old chicks
    totalCurrentWeight: '', // Total weight
    targetWeight: '',
    notes: '',
    arrivalWeather: {
      temperature: 0,
      humidity: 0,
      conditions: '',
      timestamp: ''
    },
    vaccinationSchedule: [
      { name: 'Newcastle Disease', dayNumber: 7, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 14, completed: false },
      { name: 'Gumboro Disease', dayNumber: 21, completed: false }
    ]
  };
  
  // Form validation
  let errors = {
    batchName: '',
    startDate: '',
    initialCount: '',
    breedType: '',
    weightPerBird: '',
    supplier: '',
    targetWeight: ''
  };
  let formValid = true;
  
  // Breed types available
  const breedTypes = [
    'Ross 308',
    'Cobb 500',
    'Hubbard Flex',
    'Arbor Acres Plus',
    'Ross 708'
  ];
  
  // Farm sections
  const farmSections = [
    'Section A',
    'Section B',
    'Section C',
    'Section D'
  ];
  
  // Staff members
  const staffMembers = [
    'John Doe',
    'Sarah Johnson',
    'Michael Chen',
    'Robert Wilson'
  ];
  
  // Hatcheries with their default vaccination schedules
  const hatcheries = [
    'National Chicks',
    'Ross Poultry',
    'Country Bird',
    'Astral Foods'
  ];
  
  // Supplier data with details (would normally come from a database)
  const suppliers = {
    'National Chicks': {
      address: '25 Main Road, Pretoria, 0001',
      contact: '+27 12 345 6789',
      email: 'info@nationalchicks.co.za',
      coordinates: { lat: -25.731340, lng: 28.218370 }
    },
    'Ross Poultry': {
      address: '42 Industrial Drive, Johannesburg, 2000',
      contact: '+27 11 789 0123',
      email: 'orders@rosspoultry.co.za',
      coordinates: { lat: -26.195246, lng: 28.034088 }
    },
    'Country Bird': {
      address: '15 Farm Road, Bloemfontein, 9301',
      contact: '+27 51 432 1098',
      email: 'support@countrybird.co.za',
      coordinates: { lat: -29.085216, lng: 26.159454 }
    },
    'Astral Foods': {
      address: '8 Corporate Avenue, Durban, 4001',
      contact: '+27 31 765 4321',
      email: 'info@astralfoods.co.za',
      coordinates: { lat: -29.816864, lng: 30.903916 }
    }
  };
  
  // Farm location (would normally be set in settings)
  const farmLocation = {
    address: '123 Farm Lane, Stellenbosch, 7600',
    coordinates: { lat: -33.932366, lng: 18.860152 }
  };
  
  // Default vaccination schedules by supplier (would normally come from a database)
  const vaccinationSchedules = {
    'National Chicks': [
      { name: 'Newcastle Disease', dayNumber: 7, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 14, completed: false },
      { name: 'Gumboro Disease', dayNumber: 21, completed: false }
    ],
    'Ross Poultry': [
      { name: 'Newcastle Disease', dayNumber: 7, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 14, completed: false },
      { name: 'Gumboro Disease', dayNumber: 18, completed: false },
      { name: 'Coccidiosis', dayNumber: 21, completed: false }
    ],
    'Country Bird': [
      { name: 'Newcastle Disease', dayNumber: 10, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 16, completed: false },
      { name: 'Gumboro Disease', dayNumber: 22, completed: false }
    ],
    'Astral Foods': [
      { name: 'Newcastle Disease', dayNumber: 7, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 14, completed: false },
      { name: 'Gumboro Disease', dayNumber: 21, completed: false },
      { name: 'Avian Influenza', dayNumber: 28, completed: false }
    ]
  };
  
  // Selected supplier details
  /** @type {Object|null} */
  let selectedSupplier = null;
  let travelDistance = 0;
  
  /**
   * Calculate distance between two coordinates using Haversine formula
   * @param {{lat: number, lng: number}} coord1 - First coordinates {lat, lng}
   * @param {{lat: number, lng: number}} coord2 - Second coordinates {lat, lng}
   * @returns {number} Distance in kilometers
   */
  function calculateDistance(coord1, coord2) {
    const R = 6371; // Earth's radius in km
    const dLat = (coord2.lat - coord1.lat) * Math.PI / 180;
    const dLon = (coord2.lng - coord1.lng) * Math.PI / 180;
    const a = 
      Math.sin(dLat/2) * Math.sin(dLat/2) +
      Math.cos(coord1.lat * Math.PI / 180) * Math.cos(coord2.lat * Math.PI / 180) * 
      Math.sin(dLon/2) * Math.sin(dLon/2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    return Math.round(R * c);
  }
  
  // Update supplier details when supplier changes
  function updateSupplierDetails() {
    const supplierName = batchData.sourceHatchery;
    if (supplierName && Object.prototype.hasOwnProperty.call(suppliers, supplierName)) {
      selectedSupplier = suppliers[supplierName];
      travelDistance = calculateDistance(
        selectedSupplier.coordinates,
        farmLocation.coordinates
      );
    } else {
      selectedSupplier = null;
      travelDistance = 0;
    }
  }
  
  // Update vaccination schedule when supplier changes
  function updateVaccinationSchedule(supplier) {
    if (vaccinationSchedules[supplier]) {
      batchData.vaccinationSchedule = [...vaccinationSchedules[supplier]];
    }
    updateSupplierDetails();
  }
  
  // Initialize form data and state variables
  let showVaccinationSection = false;
  /** @type {File[]} */
  let uploadedFiles = [];
  let weatherLoading = false;
  /** @type {string|null} */
  let weatherError = null;
  
  // Weight calculation functions
  function calculateTotalWeight() {
    if (batchData.currentWeight && batchData.initialCount && !isNaN(parseInt(batchData.currentWeight)) && !isNaN(parseInt(batchData.initialCount))) {
      batchData.totalCurrentWeight = String(parseInt(batchData.currentWeight) * parseInt(batchData.initialCount));
    }
  }
  
  function calculateWeightPerBird() {
    if (batchData.totalCurrentWeight && batchData.initialCount && !isNaN(parseInt(batchData.totalCurrentWeight)) && !isNaN(parseInt(batchData.initialCount))) {
      batchData.currentWeight = String(Math.round(parseInt(batchData.totalCurrentWeight) / parseInt(batchData.initialCount)));
    }
  }
  
  // Don't auto-calculate on initialization, wait for user input
  
  /**
   * Fetch current weather data for the farm location
   */
  async function fetchWeatherData() {
    weatherLoading = true;
    weatherError = null;
    
    try {
      // In a real application, you would make an API call here
      // For demo purposes, we'll simulate a weather API response
      await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate API delay
      
      // Simulate weather data (in a real app, this would come from the API)
      const currentDate = new Date();
      const weatherData = {
        temperature: Math.round(15 + Math.random() * 15), // Random temp between 15-30°C
        humidity: Math.round(50 + Math.random() * 40),    // Random humidity between 50-90%
        conditions: ['Sunny', 'Partly cloudy', 'Cloudy', 'Light rain'][Math.floor(Math.random() * 4)],
        timestamp: currentDate.toISOString()
      };
      
      // Update batch data with weather information
      batchData.arrivalWeather = weatherData;
      
      weatherLoading = false;
    } catch (error) {
      console.error('Error fetching weather data:', error);
      weatherError = 'Failed to fetch weather data. Please try again.';
      weatherLoading = false;
    }
  }
  
  // Fetch weather data when the component is initialized
  fetchWeatherData();
  
  /**
   * Handle file uploads
   * @param {Event} event - The file input change event
   */
  function handleFileUpload(event) {
    const files = event.target.files;
    if (files) {
      for (let i = 0; i < files.length; i++) {
        uploadedFiles = [...uploadedFiles, files[i]];
      }
    }
  }
  
  /**
   * Remove a file from the uploaded files list
   * @param {number} index - The index of the file to remove
   */
  function removeFile(index) {
    uploadedFiles = uploadedFiles.filter((_, i) => i !== index);
  }
  
  /**
   * Format file size for display
   * @param {number} bytes - The file size in bytes
   * @returns {string} The formatted file size
   */
  function formatFileSize(bytes) {
    if (bytes === 0) return '0 Bytes';
    const k = 1024;
    const sizes = ['Bytes', 'KB', 'MB', 'GB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
  }
  
  // Function to calculate days between dates
  function calculateDays(start, end) {
    if (!start || !end) return batchData.cycleDays;
    const startDate = new Date(start);
    const endDate = new Date(end);
    const diffTime = Math.abs(endDate - startDate);
    return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  }
  
  // Calculate cycle days when dates change
  $: if (batchData.startDate && batchData.projectedEndDate) {
    const calculatedDays = calculateDays(batchData.startDate, batchData.projectedEndDate);
    if (!isNaN(calculatedDays)) {
      batchData.cycleDays = calculatedDays;
    }
  }
  
  // Add a new vaccination
  function addVaccination() {
    batchData.vaccinationSchedule = [
      ...batchData.vaccinationSchedule,
      { name: '', dayNumber: 28, completed: false }
    ];
  }
  
  // Remove a vaccination
  function removeVaccination(index) {
    batchData.vaccinationSchedule = batchData.vaccinationSchedule.filter((_, i) => i !== index);
  }
  
  // Validate the form
  function validateForm() {
    let isValid = true;
    
    // Reset errors
    errors = {
      batchName: '',
      startDate: '',
      initialCount: '',
      breedType: '',
      weightPerBird: '',
      supplier: '',
      targetWeight: ''
    };
    
    // Validate batch name
    if (!batchData.name) {
      errors.batchName = 'Batch name is required';
      isValid = false;
    }
    
    // Validate start date
    if (!batchData.startDate) {
      errors.startDate = 'Start date is required';
      isValid = false;
    }
    
    // Validate initial bird count
    if (!batchData.initialCount) {
      errors.initialCount = 'Initial bird count is required';
      isValid = false;
    } else if (isNaN(Number(batchData.initialCount)) || Number(batchData.initialCount) <= 0) {
      errors.initialCount = 'Initial bird count must be a positive number';
      isValid = false;
    }
    
    // Validate breed type
    if (!batchData.breedType) {
      errors.breedType = 'Breed type is required';
      isValid = false;
    }
    
    // Validate weight per bird
    if (!batchData.currentWeight) {
      errors.weightPerBird = 'Weight per bird is required';
      isValid = false;
    } else if (isNaN(Number(batchData.currentWeight)) || Number(batchData.currentWeight) <= 0) {
      errors.weightPerBird = 'Weight per bird must be a positive number';
      isValid = false;
    }
    
    // Validate supplier
    if (!batchData.sourceHatchery) {
      errors.supplier = 'Supplier is required';
      isValid = false;
    }
    
    // Target weight is optional as it will come from settings
    if (batchData.targetWeight && (isNaN(Number(batchData.targetWeight)) || Number(batchData.targetWeight) <= 0)) {
      errors.targetWeight = 'Target weight must be a positive number';
      isValid = false;
    }
    
    return isValid;
  }
  
  // Handle form submission
  function handleSubmit() {
    if (validateForm()) {
      dispatch('submit', batchData);
    }
  }
  
  // Handle form cancellation
  function handleCancel() {
    dispatch('cancel');
  }
  
  // Update projected end date when start date or cycle days change manually
  function updateProjectedEndDate() {
    if (batchData.startDate) {
      const startDate = new Date(batchData.startDate);
      startDate.setDate(startDate.getDate() + batchData.cycleDays);
      batchData.projectedEndDate = startDate.toISOString().split('T')[0];
    }
  }
  
  // Watch for changes to start date or cycle days
  $: if (batchData.startDate) {
    updateProjectedEndDate();
  }
  
  $: if (batchData.cycleDays) {
    updateProjectedEndDate();
  }
  
  // Update batch ID when year or batch number changes
  $: {
    const year = new Date(batchData.startDate).getFullYear();
    batchData.id = `B-${year}-${nextBatchNumber}`;
  }
</script>

<div class="batch-form-container">
  <form on:submit|preventDefault={handleSubmit} class="batch-form">
    <div class="form-header">
      <h2>Add New Batch</h2>
      <p class="form-subtitle">Enter details for the new broiler batch</p>
    </div>
    
    <div class="form-container">
      <div class="section-divider"></div>
      <!-- Basic Information -->
      <div class="form-section">
        <h3 class="section-title">Basic Information</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label for="batchId">Batch ID</label>
            <input 
              type="text" 
              id="batchId" 
              class="form-control" 
              bind:value={batchData.id} 
              readonly
            />
            <small class="form-text">Auto-generated ID</small>
          </div>
          
          <div class="form-group">
            <label for="batchName">Batch Name *</label>
            <input 
              type="text" 
              id="batchName" 
              class="form-control {errors.batchName ? 'is-invalid' : ''}" 
              bind:value={batchData.name} 
              required
            />
            {#if errors.batchName}
              <div class="invalid-feedback">{errors.batchName}</div>
            {/if}
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label for="startDate">Start Date *</label>
            <input 
              type="date" 
              id="startDate" 
              class="form-control {errors.startDate ? 'is-invalid' : ''}" 
              bind:value={batchData.startDate} 
              required
            />
            {#if errors.startDate}
              <div class="invalid-feedback">{errors.startDate}</div>
            {/if}
          </div>
          
          <div class="form-group">
            <label for="cycleDays">Cycle Length (days)</label>
            <input 
              type="number" 
              id="cycleDays" 
              class="form-control" 
              bind:value={batchData.cycleDays} 
              min="1" 
              max="120"
            />
          </div>
          
          <div class="form-group">
            <label for="projectedEndDate">Projected End Date</label>
            <input 
              type="date" 
              id="projectedEndDate" 
              class="form-control" 
              bind:value={batchData.projectedEndDate} 
              readonly
            />
            <small class="form-text">Auto-calculated</small>
          </div>
        </div>
      </div>
      
      <div class="section-divider"></div>
      
      <!-- Supplier Information -->
      <div class="form-section">
        <h3 class="section-title">Supplier Information</h3>
        
        <div class="form-group">
          <label for="sourceHatchery">Source Hatchery *</label>
          <select 
            id="sourceHatchery" 
            class="form-control {errors.supplier ? 'is-invalid' : ''}" 
            bind:value={batchData.sourceHatchery}
            on:change={() => updateVaccinationSchedule(batchData.sourceHatchery)}
            required
          >
            <option value="">Select a supplier</option>
            {#each Object.keys(suppliers) as supplier}
              <option value={supplier}>{supplier}</option>
            {/each}
          </select>
          {#if errors.supplier}
            <div class="invalid-feedback">{errors.supplier}</div>
          {/if}
          <small class="form-text">Vaccination schedule will update based on supplier</small>
        </div>
        
        {#if selectedSupplier}
          <div class="supplier-card">
            <div class="supplier-card-content">
              <div class="supplier-info">
                <div class="supplier-info-item">
                  <span class="material-icons">location_on</span>
                  <span>{selectedSupplier.address}</span>
                </div>
                <div class="supplier-info-row">
                  <div class="supplier-info-item">
                    <span class="material-icons">phone</span>
                    <span>{selectedSupplier.contact}</span>
                  </div>
                  <div class="supplier-info-item">
                    <span class="material-icons">email</span>
                    <span>{selectedSupplier.email}</span>
                  </div>
                </div>
              </div>
              <div class="travel-distance-compact">
                <div class="distance-badge">
                  <span class="material-icons">route</span>
                  <span class="distance-value">{travelDistance} km</span>
                </div>
                <small>Distance from supplier to farm</small>
              </div>
            </div>
          </div>
        {/if}
      </div>
      
      <div class="section-divider"></div>
      
      <!-- Flock Details -->
      <div class="form-section">
        <h3 class="section-title">Flock Details</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label for="initialCount">Initial Bird Count *</label>
            <input 
              type="number" 
              id="initialCount" 
              class="form-control {errors.initialCount ? 'is-invalid' : ''}" 
              bind:value={batchData.initialCount} 
              min="1" 
              placeholder="Enter number of birds"
              required
            />
            {#if errors.initialCount}
              <div class="invalid-feedback">{errors.initialCount}</div>
            {/if}
          </div>
          
          <div class="form-group">
            <label for="deadOnArrival">Dead on Arrival (DOA)</label>
            <input 
              type="number" 
              id="deadOnArrival" 
              class="form-control" 
              bind:value={batchData.deadOnArrival} 
              min="0" 
              placeholder="Number of birds dead on arrival"
            />
            <small class="form-text">Number of birds that arrived dead</small>
          </div>
          
          <div class="form-group">
            <label for="breedType">Breed Type *</label>
            <select 
              id="breedType" 
              class="form-control {errors.breedType ? 'is-invalid' : ''}" 
              bind:value={batchData.breedType}
              required
            >
              <option value="">Select breed type</option>
              {#each breedTypes as breedType}
                <option value={breedType}>{breedType}</option>
              {/each}
            </select>
            {#if errors.breedType}
              <div class="invalid-feedback">{errors.breedType}</div>
            {/if}
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label for="currentWeight">Weight per Bird (g) *</label>
            <div class="input-group">
              <input 
                type="number" 
                id="currentWeight" 
                class="form-control {errors.weightPerBird ? 'is-invalid' : ''}" 
                bind:value={batchData.currentWeight} 
                on:input={() => calculateTotalWeight()}
                min="1" 
                placeholder="Enter weight per bird"
                required
              />
              {#if errors.weightPerBird}
                <div class="invalid-feedback">{errors.weightPerBird}</div>
              {/if}
              <button 
                class="btn btn-outline-secondary" 
                type="button"
                on:click={() => calculateWeightPerBird()}
                title="Calculate weight per bird from total weight"
              >
                <span class="material-icons">calculate</span>
              </button>
            </div>
            <small class="form-text">Initial weight of day-old chicks</small>
          </div>
          
          <div class="form-group">
            <label for="totalCurrentWeight">Total Weight (g)</label>
            <div class="input-group">
              <input 
                type="number" 
                id="totalCurrentWeight" 
                class="form-control" 
                bind:value={batchData.totalCurrentWeight} 
                on:input={() => calculateWeightPerBird()}
                min="1"
                placeholder="Enter total weight"
              />
              <button 
                class="btn btn-outline-secondary" 
                type="button"
                on:click={() => calculateTotalWeight()}
                title="Calculate total weight from weight per bird"
              >
                <span class="material-icons">calculate</span>
              </button>
            </div>
            <small class="form-text">Total weight of all birds</small>
          </div>
          
          <div class="form-group">
            <label for="targetWeight">Target Weight (g)</label>
            <input 
              type="number" 
              id="targetWeight" 
              class="form-control {errors.targetWeight ? 'is-invalid' : ''}" 
              bind:value={batchData.targetWeight} 
              min="1" 
              placeholder="From settings"
              disabled
            />
            {#if errors.targetWeight}
              <div class="invalid-feedback">{errors.targetWeight}</div>
            {/if}
            <small class="form-text">Target weight will be set in settings</small>
          </div>
        </div>
      </div>
      
      <div class="section-divider"></div>
      
      <!-- Farm Section & Arrival Weather -->
      <div class="form-section">
        <h3 class="section-title">Farm Section</h3>
        
        <div class="form-group">
          <label for="farmSection">Farm Section</label>
          <select 
            id="farmSection" 
            class="form-control" 
            bind:value={batchData.farmSection}
          >
            {#each farmSections as section}
              <option value={section}>{section}</option>
            {/each}
          </select>
        </div>
        
        <div class="form-group">
          <label>Arrival Weather <small>(auto-collected)</small></label>
          
          {#if weatherLoading}
            <div class="weather-card loading">
              <div class="weather-card-content">
                <span class="material-icons rotating">sync</span>
                <span>Loading weather data...</span>
              </div>
            </div>
          {:else if weatherError}
            <div class="weather-card error">
              <div class="weather-card-content">
                <div class="weather-error-message">
                  <span class="material-icons">error_outline</span>
                  <span>{weatherError}</span>
                </div>
                <button type="button" class="btn btn-sm btn-outline-primary" on:click={fetchWeatherData}>
                  <span class="material-icons">refresh</span> Retry
                </button>
              </div>
            </div>
          {:else if batchData.arrivalWeather.temperature > 0}
            <div class="weather-card">
              <div class="weather-card-content">
                <div class="weather-info">
                  <div class="weather-badges">
                    <div class="weather-badge">
                      <span class="material-icons">thermostat</span>
                      <span class="badge-label">Temperature:</span>
                      <span>{batchData.arrivalWeather.temperature}°C</span>
                    </div>
                    <div class="weather-badge">
                      <span class="material-icons">water_drop</span>
                      <span class="badge-label">Humidity:</span>
                      <span>{batchData.arrivalWeather.humidity}%</span>
                    </div>
                    <div class="weather-badge conditions">
                      <span class="material-icons">cloud</span>
                      <span class="badge-label">Conditions:</span>
                      <span>{batchData.arrivalWeather.conditions}</span>
                    </div>
                  </div>
                  <div class="weather-timestamp">
                    <small>Recorded: {batchData.arrivalWeather.timestamp ? new Date(batchData.arrivalWeather.timestamp).toLocaleString() : ''}</small>
                    <button type="button" class="btn btn-sm btn-outline-primary btn-xs refresh-btn" on:click={fetchWeatherData}>
                      <span class="material-icons">refresh</span>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          {:else}
            <div class="weather-card empty">
              <div class="weather-card-content">
                <div class="weather-empty-message">
                  <span class="material-icons">wb_sunny</span>
                  <span>No weather data available</span>
                </div>
                <button type="button" class="btn btn-sm btn-primary" on:click={fetchWeatherData}>
                  <span class="material-icons">refresh</span> Get Weather
                </button>
              </div>
            </div>
          {/if}
        </div>
      </div>
      
      <div class="section-divider"></div>
      
      <!-- Additional Notes -->
      <div class="form-section">
        <h3 class="section-title">Additional Notes</h3>
        
        <div class="form-group">
          <textarea 
            id="notes" 
            class="form-control" 
            bind:value={batchData.notes} 
            rows="4" 
            placeholder="Enter any additional notes or special instructions..."
          ></textarea>
        </div>
      </div>
      
      <div class="section-divider"></div>
      
      <!-- Document Upload -->
      <div class="form-section">
        <h3 class="section-title">Documents</h3>
        <p class="section-description">Upload relevant documents such as delivery notes, certificates, or invoices.</p>
        
        <div class="document-upload">
          <div class="upload-area">
            <label for="documentUpload" class="upload-label">
              <span class="material-icons">upload_file</span>
              <span>Click to upload or drag files here</span>
            </label>
            <input 
              type="file" 
              id="documentUpload" 
              class="file-input" 
              multiple
              on:change={handleFileUpload}
            />
          </div>
          
          {#if uploadedFiles.length > 0}
            <div class="uploaded-files">
              <h4>Uploaded Documents</h4>
              <ul class="file-list">
                {#each uploadedFiles as file, index}
                  <li class="file-item">
                    <span class="material-icons file-icon">description</span>
                    <span class="file-name">{file.name}</span>
                    <span class="file-size">({formatFileSize(file.size)})</span>
                    <button 
                      type="button" 
                      class="btn btn-sm btn-outline-danger" 
                      on:click={() => removeFile(index)}
                    >
                      <span class="material-icons">delete</span>
                    </button>
                  </li>
                {/each}
              </ul>
            </div>
          {/if}
        </div>
      </div>
      
      <!-- Vaccination Schedule (Optional) -->
      <div class="form-section">
        <div class="section-header">
          <h3 class="section-title">Vaccination Schedule (Optional)</h3>
          <div class="form-check form-switch">
            <input 
              class="form-check-input" 
              type="checkbox" 
              id="showVaccinations" 
              bind:checked={showVaccinationSection}
            />
            <label class="form-check-label" for="showVaccinations">
              {showVaccinationSection ? 'Hide Vaccinations' : 'Show Vaccinations'}
            </label>
          </div>
        </div>
        
        {#if showVaccinationSection}
          <p class="section-description">Default schedule based on supplier. You can modify as needed.</p>
          
          <div class="vaccination-list">
            {#each batchData.vaccinationSchedule as vaccination, index}
              <div class="vaccination-item">
                <div class="vaccination-details">
                  <input 
                    type="text" 
                    class="form-control" 
                    bind:value={vaccination.name} 
                    placeholder="Vaccine name"
                  />
                  <input 
                    type="number" 
                    class="form-control" 
                    bind:value={vaccination.dayNumber} 
                    min="1" 
                    max="100" 
                    placeholder="Day"
                  />
                  <div class="form-check">
                    <input 
                      type="checkbox" 
                      class="form-check-input" 
                      id={`completed-${index}`} 
                      bind:checked={vaccination.completed}
                    />
                    <label class="form-check-label" for={`completed-${index}`}>Completed</label>
                  </div>
                </div>
                <button 
                  type="button" 
                  class="btn btn-sm btn-outline-danger" 
                  on:click={() => removeVaccination(index)}
                >
                  <span class="material-icons">delete</span>
                </button>
              </div>
            {/each}
            
            {#if batchData.vaccinationSchedule.length === 0}
              <p class="text-muted">No vaccinations scheduled. Click "Add Vaccination" to add one.</p>
            {/if}
            
            <button type="button" class="btn btn-sm btn-outline-primary mt-2" on:click={addVaccination}>
              <span class="material-icons">add</span> Add Vaccination
            </button>
          </div>
        {/if}
      </div>
    </div>
    
    <div class="form-actions">
      <button type="button" class="btn btn-outline-secondary" on:click={handleCancel}>
        Cancel
      </button>
      <button type="submit" class="btn btn-primary">
        <span class="material-icons">save</span>
        Create Batch
      </button>
    </div>
  </form>
</div>

<style>
  .batch-form-container {
    max-width: 1000px;
    margin: 0 auto;
    padding: 20px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  }
  
  .form-header {
    margin-bottom: 24px;
    border-bottom: 1px solid #eee;
    padding-bottom: 16px;
  }
  
  .form-header h2 {
    margin: 0;
    font-size: 24px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .form-subtitle {
    margin: 8px 0 0;
    color: var(--gray);
    font-size: 14px;
  }
  
  .form-sections {
    display: flex;
    flex-direction: column;
    gap: 24px;
  }
  
  .form-section {
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 20px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
  }
  
  .section-title {
    font-size: 16px;
    font-weight: 600;
    margin: 0 0 16px 0;
    color: var(--dark);
  }
  
  .form-row {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
    margin-bottom: 16px;
  }
  
  .form-row:last-child {
    margin-bottom: 0;
  }
  
  .form-group {
    flex: 1;
    min-width: 200px;
  }
  
  .action-group {
    flex: 0 0 auto;
    min-width: auto;
    align-self: flex-end;
  }
  
  label {
    display: block;
    margin-bottom: 6px;
    font-weight: 500;
    font-size: 14px;
    color: var(--dark);
  }
  
  .form-control, .form-select {
    width: 100%;
    padding: 10px 12px;
    border: 1px solid #ddd;
    border-radius: 6px;
    font-size: 14px;
    transition: border-color 0.2s;
  }
  
  .form-control:focus, .form-select:focus {
    border-color: var(--primary);
    outline: none;
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
  
  .vaccination-list {
    margin-bottom: 16px;
  }
  
  .vaccination-item {
    background-color: white;
    border-radius: 6px;
    padding: 12px;
    margin-bottom: 8px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
  }
  
  .vaccination-item .form-row {
    margin-bottom: 0;
  }
  
  .vaccination-details {
    display: flex;
    gap: 10px;
    flex: 1;
  }
  
  .section-divider {
    height: 1px;
    background-color: #e0e0e0;
    margin: 20px 0;
    width: 100%;
  }
  
  .document-upload {
    margin-top: 15px;
  }
  
  .upload-area {
    border: 2px dashed #ccc;
    border-radius: 5px;
    padding: 30px;
    text-align: center;
    margin-bottom: 20px;
    cursor: pointer;
    transition: border-color 0.3s;
  }
  
  .upload-area:hover {
    border-color: #007bff;
  }
  
  .upload-label {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
    cursor: pointer;
    width: 100%;
  }
  
  .upload-label .material-icons {
    font-size: 48px;
    color: #6c757d;
  }
  
  .file-input {
    display: none;
  }
  
  .uploaded-files {
    margin-top: 20px;
  }
  
  .file-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  
  .file-item {
    display: flex;
    align-items: center;
    padding: 10px;
    border: 1px solid #eee;
    border-radius: 4px;
    margin-bottom: 10px;
  }
  
  .file-icon {
    margin-right: 10px;
    color: #6c757d;
  }
  
  .file-name {
    flex: 1;
    font-weight: 500;
  }
  
  .file-size {
    color: #6c757d;
    margin-right: 10px;
  }
  
  .supplier-card {
    margin-top: 10px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    overflow: hidden;
    background-color: white;
    border: 1px solid #e0e0e0;
  }
  
  .supplier-card-content {
    padding: 12px;
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
  
  .supplier-info {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
  
  .supplier-info-row {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .supplier-info-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.9rem;
  }
  
  .supplier-info-item .material-icons {
    font-size: 16px;
    color: #6c757d;
  }
  
  .travel-distance-compact {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin-top: 8px;
    padding-top: 8px;
    border-top: 1px dashed #e0e0e0;
  }
  
  .distance-badge {
    display: inline-flex;
    align-items: center;
    background-color: #e6f2ff;
    color: #007bff;
    padding: 4px 8px;
    border-radius: 16px;
    font-weight: 500;
    gap: 4px;
    margin-bottom: 4px;
  }
  
  .distance-badge .material-icons {
    font-size: 16px;
  }
  
  .distance-value {
    font-weight: 600;
  }
  
  @media (min-width: 768px) {
    .supplier-card-content {
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
    }
    
    .travel-distance-compact {
      margin-top: 0;
      padding-top: 0;
      border-top: none;
      border-left: 1px dashed #e0e0e0;
      padding-left: 16px;
      min-width: 150px;
    }
  }
  
  .weather-card {
    margin-top: 10px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    overflow: hidden;
    background-color: white;
    border: 1px solid #e0e0e0;
  }
  
  .weather-card.loading .material-icons,
  .weather-card.error .material-icons {
    font-size: 20px;
    margin-right: 8px;
  }
  
  .weather-card.loading .material-icons {
    color: #007bff;
  }
  
  .weather-card.error .material-icons {
    color: #dc3545;
  }
  
  .weather-card.empty .material-icons {
    color: #ffc107;
    font-size: 20px;
    margin-right: 8px;
  }
  
  .weather-card-content {
    padding: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  
  .weather-info {
    width: 100%;
  }
  
  .weather-badges {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 8px;
  }
  
  .weather-badge {
    display: inline-flex;
    align-items: center;
    background-color: #f0f7ff;
    padding: 4px 10px;
    border-radius: 16px;
    font-weight: 500;
    gap: 4px;
  }
  
  .badge-label {
    font-weight: normal;
    color: #555;
    margin-right: 2px;
  }
  
  .weather-badge .material-icons {
    font-size: 16px;
    color: #007bff;
  }
  
  .weather-badge.conditions {
    background-color: #f0f9ff;
  }
  
  .weather-badge.conditions .material-icons {
    color: #0099cc;
  }
  
  .weather-timestamp {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 11px;
    color: #6c757d;
  }
  
  .refresh-btn {
    padding: 2px 6px;
    height: 24px;
  }
  
  .refresh-btn .material-icons {
    font-size: 14px;
  }
  
  .weather-error-message,
  .weather-empty-message {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
  }
  
  .btn-xs {
    padding: 0 4px;
    font-size: 0.75rem;
    line-height: 1.5;
    border-radius: 0.2rem;
  }
  
  .rotating {
    animation: rotate 2s linear infinite;
  }
  
  @keyframes rotate {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 12px;
    margin-top: 24px;
    padding-top: 16px;
    border-top: 1px solid #eee;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 10px 16px;
    border-radius: 6px;
    font-weight: 500;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
    border: none;
  }
  
  .btn-sm {
    padding: 6px 12px;
    font-size: 13px;
  }
  
  .btn .material-icons {
    font-size: 16px;
    margin-right: 6px;
  }
  
  .btn-primary {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-primary:hover {
    background-color: #1565c0;
    box-shadow: 0 2px 4px rgba(21, 101, 192, 0.2);
  }
  
  .btn-outline-secondary {
    background-color: transparent;
    border: 1px solid #ddd;
    color: var(--gray);
  }
  
  .btn-outline-secondary:hover {
    background-color: #f5f5f5;
  }
  
  .btn-outline-primary {
    background-color: transparent;
    border: 1px solid var(--primary);
    color: var(--primary);
  }
  
  .btn-outline-primary:hover {
    background-color: rgba(25, 118, 210, 0.05);
  }
  
  .btn-outline-danger {
    background-color: transparent;
    border: 1px solid var(--danger);
    color: var(--danger);
  }
  
  .btn-outline-danger:hover {
    background-color: rgba(244, 67, 54, 0.05);
  }
  
  @media (max-width: 768px) {
    .form-row {
      flex-direction: column;
      gap: 12px;
    }
    
    .form-group {
      width: 100%;
    }
  }
</style>
