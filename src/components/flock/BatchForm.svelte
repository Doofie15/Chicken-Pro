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
    breedType: 'Ross 308',
    farmSection: 'Section A',
    sourceHatchery: 'National Chicks',
    feedType: 'Standard',
    currentWeight: '', // Initial weight of day-old chicks
    totalCurrentWeight: '', // Total weight
    targetWeight: '',
    notes: '',
    vaccinationSchedule: [
      { name: 'Newcastle Disease', dayNumber: 7, completed: false },
      { name: 'Infectious Bronchitis', dayNumber: 14, completed: false },
      { name: 'Gumboro Disease', dayNumber: 21, completed: false }
    ],
    responsibleStaff: 'John Doe',
    housingType: 'Closed House',
    environmentalControls: {
      temperature: 32,
      humidity: 65,
      lightHours: 23
    }
  };
  
  // Form validation
  let errors = {
    initialCount: '',
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
  
  // Housing types
  const housingTypes = [
    'Closed House',
    'Open-Sided House',
    'Free Range',
    'Semi-Intensive'
  ];
  
  // Feed types
  const feedTypes = [
    'Standard',
    'Premium',
    'Organic',
    'Antibiotic-Free'
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
  
  // Default vaccination schedules by supplier
  const vaccinationSchedulesBySupplier = {
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
  
  // Update vaccination schedule when supplier changes
  function updateVaccinationSchedule(supplier) {
    if (vaccinationSchedulesBySupplier[supplier]) {
      batchData.vaccinationSchedule = [...vaccinationSchedulesBySupplier[supplier]];
    }
  }
  
  // Initialize cycle days
  let cycleDays = 42; // Default 42-day cycle
  
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
  
  // Function to calculate days between dates
  function calculateDays(start, end) {
    if (!start || !end) return cycleDays;
    const startDate = new Date(start);
    const endDate = new Date(end);
    const diffTime = Math.abs(endDate - startDate);
    return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  }
  
  // Calculate cycle days when dates change
  $: if (batchData.startDate && batchData.projectedEndDate) {
    const calculatedDays = calculateDays(batchData.startDate, batchData.projectedEndDate);
    if (!isNaN(calculatedDays)) {
      cycleDays = calculatedDays;
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
      initialBirdCount: '',
      targetWeight: ''
    };
    
    // Validate initial bird count
    if (!batchData.initialCount) {
      errors.initialCount = 'Initial bird count is required';
      isValid = false;
    } else if (isNaN(parseInt(batchData.initialCount)) || parseInt(batchData.initialCount) <= 0) {
      errors.initialCount = 'Initial bird count must be a positive number';
      isValid = false;
    }
    
    // Target weight is optional as it will come from settings
    if (batchData.targetWeight && (isNaN(parseInt(batchData.targetWeight)) || parseInt(batchData.targetWeight) <= 0)) {
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
      startDate.setDate(startDate.getDate() + cycleDays);
      batchData.projectedEndDate = startDate.toISOString().split('T')[0];
    }
  }
  
  // Watch for changes to start date or cycle days
  $: if (batchData.startDate) {
    updateProjectedEndDate();
  }
  
  $: if (cycleDays) {
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
    
    <div class="form-sections">
      <!-- Basic Information Section -->
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
            <small class="form-text">Auto-generated batch ID</small>
          </div>
          
          <div class="form-group">
            <label for="batchName">Batch Name*</label>
            <input 
              type="text" 
              id="batchName" 
              class="form-control" 
              bind:value={batchData.name} 
              required
            />
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label for="startDate">Start Date*</label>
            <input 
              type="date" 
              id="startDate" 
              class="form-control" 
              bind:value={batchData.startDate} 
              required
            />
          </div>
          
          <div class="form-group">
            <label for="cycleDays">Cycle Length (days)</label>
            <input 
              type="number" 
              id="cycleDays" 
              class="form-control" 
              bind:value={cycleDays} 
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
            <small class="form-text">Auto-calculated based on cycle length</small>
          </div>
        </div>
      </div>
      
      <!-- Flock Details Section -->
      <div class="form-section">
        <h3 class="section-title">Flock Details</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label for="initialCount">Initial Bird Count*</label>
            <input 
              type="number" 
              id="initialCount" 
              class="form-control" 
              class:is-invalid={errors.initialCount}
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
            <label for="breedType">Breed Type</label>
            <select 
              id="breedType" 
              class="form-select" 
              bind:value={batchData.breedType}
            >
              {#each breedTypes as breed}
                <option value={breed}>{breed}</option>
              {/each}
            </select>
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label for="currentWeight">Weight per Bird (g)*</label>
            <div class="input-group">
              <input 
                type="number" 
                id="currentWeight" 
                class="form-control" 
                bind:value={batchData.currentWeight} 
                on:input={() => calculateTotalWeight()}
                min="1" 
                required
              />
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
              class="form-control" 
              class:is-invalid={errors.targetWeight}
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
      
      <!-- Housing & Environment Section -->
      <div class="form-section">
        <h3 class="section-title">Housing & Environment</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label for="farmSection">Farm Section</label>
            <select 
              id="farmSection" 
              class="form-select" 
              bind:value={batchData.farmSection}
            >
              {#each farmSections as section}
                <option value={section}>{section}</option>
              {/each}
            </select>
          </div>
          
          <div class="form-group">
            <label for="housingType">Housing Type</label>
            <select 
              id="housingType" 
              class="form-select" 
              bind:value={batchData.housingType}
            >
              {#each housingTypes as housing}
                <option value={housing}>{housing}</option>
              {/each}
            </select>
          </div>
        </div>
        
        <div class="form-row">
          <div class="form-group">
            <label for="temperature">Initial Temperature (°C)</label>
            <input 
              type="number" 
              id="temperature" 
              class="form-control" 
              bind:value={batchData.environmentalControls.temperature} 
              min="20" 
              max="40" 
              step="0.1"
            />
          </div>
          
          <div class="form-group">
            <label for="humidity">Initial Humidity (%)</label>
            <input 
              type="number" 
              id="humidity" 
              class="form-control" 
              bind:value={batchData.environmentalControls.humidity} 
              min="40" 
              max="80" 
              step="1"
            />
          </div>
          
          <div class="form-group">
            <label for="lightHours">Light Hours (per day)</label>
            <input 
              type="number" 
              id="lightHours" 
              class="form-control" 
              bind:value={batchData.environmentalControls.lightHours} 
              min="1" 
              max="24" 
              step="1"
            />
          </div>
        </div>
      </div>
      
      <!-- Supply & Management Section -->
      <div class="form-section">
        <h3 class="section-title">Supply & Management</h3>
        
        <div class="form-row">
          <div class="form-group">
            <label for="sourceHatchery">Source Hatchery</label>
            <select 
              id="sourceHatchery" 
              class="form-select" 
              bind:value={batchData.sourceHatchery}
              on:change={() => updateVaccinationSchedule(batchData.sourceHatchery)}
            >
              {#each hatcheries as hatchery}
                <option value={hatchery}>{hatchery}</option>
              {/each}
            </select>
            <small class="form-text">Vaccination schedule will update based on supplier</small>
          </div>
          
          <div class="form-group">
            <label for="feedType">Feed Type</label>
            <select 
              id="feedType" 
              class="form-select" 
              bind:value={batchData.feedType}
            >
              {#each feedTypes as feed}
                <option value={feed}>{feed}</option>
              {/each}
            </select>
          </div>
          
          <div class="form-group">
            <label for="responsibleStaff">Responsible Staff</label>
            <select 
              id="responsibleStaff" 
              class="form-select" 
              bind:value={batchData.responsibleStaff}
            >
              {#each staffMembers as staff}
                <option value={staff}>{staff}</option>
              {/each}
            </select>
          </div>
        </div>
      </div>
      
      <!-- Vaccination Schedule Section -->
      <div class="form-section">
        <h3 class="section-title">Vaccination Schedule</h3>
        
        <div class="vaccination-list">
          {#each batchData.vaccinationSchedule as vaccination, index}
            <div class="vaccination-item">
              <div class="form-row">
                <div class="form-group">
                  <label for={`vaccineName${index}`}>Vaccine Name</label>
                  <input 
                    type="text" 
                    id={`vaccineName${index}`} 
                    class="form-control" 
                    bind:value={vaccination.name}
                  />
                </div>
                
                <div class="form-group">
                  <label for={`vaccineDay${index}`}>Day Number</label>
                  <input 
                    type="number" 
                    id={`vaccineDay${index}`} 
                    class="form-control" 
                    bind:value={vaccination.dayNumber} 
                    min="1" 
                    max="120"
                  />
                </div>
                
                <div class="form-group action-group">
                  <button 
                    type="button" 
                    class="btn btn-outline-danger btn-sm" 
                    on:click={() => removeVaccination(index)}
                  >
                    <span class="material-icons">delete</span>
                  </button>
                </div>
              </div>
            </div>
          {/each}
        </div>
        
        <button 
          type="button" 
          class="btn btn-outline-primary btn-sm" 
          on:click={addVaccination}
        >
          <span class="material-icons">add</span>
          Add Vaccination
        </button>
      </div>
      
      <!-- Notes Section -->
      <div class="form-section">
        <h3 class="section-title">Additional Notes</h3>
        
        <div class="form-group">
          <textarea 
            id="notes" 
            class="form-control" 
            bind:value={batchData.notes} 
            rows="3" 
            placeholder="Enter any additional notes or special instructions for this batch..."
          ></textarea>
        </div>
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
