<script>
  import { onMount } from 'svelte';
  import { page } from '$app/stores';
  
  // Tab navigation
  let activeTab = 'overview';
  const tabs = [
    { id: 'overview', label: 'Overview', icon: 'dashboard' },
    { id: 'growth', label: 'Growth & Performance', icon: 'trending_up' },
    { id: 'feed', label: 'Feed Management', icon: 'restaurant' },
    { id: 'health', label: 'Health & Mortality', icon: 'healing' },
    { id: 'environment', label: 'Environment', icon: 'thermostat' },
    { id: 'financial', label: 'Financial', icon: 'attach_money' },
    { id: 'documents', label: 'Documents & Notes', icon: 'description' }
  ];
  
  // Get batch ID from URL
  $: batchId = $page.params.id;
  
  // Sample batch data - in a real app, this would come from an API call
  let batch = {
    id: 'B-2023-45',
    name: 'Batch 45',
    startDate: '2023-10-15',
    age: 28,
    initialCount: 6500,
    currentCount: 6370,
    mortality: 2.0,
    avgWeight: 1450,
    targetWeight: 2500,
    progress: 58,
    feedConversion: 1.65,
    status: 'healthy',
    supplier: 'National Chicks',
    breedType: 'Ross 308',
    projectedHarvestDate: '2023-12-10',
    housing: 'House 3',
    feedData: {
      totalConsumption: 12500,
      dailyAverage: 450,
      feedTypes: [
        { type: 'Starter', amount: 3200, cost: 1280 },
        { type: 'Grower', amount: 9300, cost: 3720 }
      ],
      feedCosts: {
        total: 5000,
        perBird: 0.78
      }
    },
    financialData: {
      totalCosts: 8750,
      projectedRevenue: 15925,
      projectedProfit: 7175,
      costBreakdown: {
        feed: 5000,
        chicks: 2600,
        labor: 650,
        utilities: 350,
        medications: 150
      }
    },
    environmentData: {
      temperature: {
        current: 26.5,
        target: 26.0,
        history: [
          { date: '2023-10-15', value: 33.0 },
          { date: '2023-10-22', value: 30.0 },
          { date: '2023-10-29', value: 28.0 },
          { date: '2023-11-05', value: 26.5 }
        ]
      },
      humidity: {
        current: 65,
        target: 60,
        history: [
          { date: '2023-10-15', value: 70 },
          { date: '2023-10-22', value: 68 },
          { date: '2023-10-29', value: 65 },
          { date: '2023-11-05', value: 65 }
        ]
      },
      lighting: {
        program: '18 hours on / 6 hours off',
        intensity: '80%'
      },
      ventilation: 'Automatic, 30% capacity'
    },
    healthData: {
      vaccinations: [
        { date: '2023-10-15', type: 'Newcastle Disease', status: 'Completed' },
        { date: '2023-10-22', type: 'Infectious Bronchitis', status: 'Completed' },
        { date: '2023-11-05', type: 'Gumboro Disease', status: 'Completed' }
      ],
      mortalityLog: [
        { date: '2023-10-16', count: 35, cause: 'Stress' },
        { date: '2023-10-23', count: 42, cause: 'Respiratory' },
        { date: '2023-11-01', count: 28, cause: 'Unknown' },
        { date: '2023-11-08', count: 25, cause: 'Culling' }
      ],
      treatments: [
        { date: '2023-10-24', type: 'Antibiotics', reason: 'Respiratory infection', duration: '5 days' }
      ]
    },
    growthData: {
      weightHistory: [
        { day: 1, weight: 42, target: 42 },
        { day: 7, weight: 170, target: 180 },
        { day: 14, weight: 430, target: 460 },
        { day: 21, weight: 850, target: 920 },
        { day: 28, weight: 1450, target: 1500 }
      ],
      feedIntakeHistory: [
        { day: 1, amount: 12 },
        { day: 7, amount: 160 },
        { day: 14, amount: 320 },
        { day: 21, amount: 580 },
        { day: 28, amount: 850 }
      ],
      fcrHistory: [
        { day: 7, value: 0.94 },
        { day: 14, value: 1.21 },
        { day: 21, value: 1.45 },
        { day: 28, value: 1.65 }
      ]
    },
    notes: [
      { date: '2023-10-15', author: 'John Smith', content: 'Chicks arrived in good condition. Initial setup complete.' },
      { date: '2023-10-24', author: 'Maria Garcia', content: 'Noticed some respiratory issues. Started treatment.' },
      { date: '2023-11-05', author: 'John Smith', content: 'Birds recovering well. Growth back on track.' }
    ],
    documents: [
      { name: 'Delivery Receipt', date: '2023-10-15', type: 'pdf' },
      { name: 'Vaccination Certificate', date: '2023-10-22', type: 'pdf' },
      { name: 'Feed Analysis Report', date: '2023-11-01', type: 'xlsx' }
    ]
  };
  
  // Format date for display
  function formatDate(dateString) {
    const date = new Date(dateString);
    return new Intl.DateTimeFormat('en-US', { 
      month: 'short', 
      day: 'numeric', 
      year: 'numeric' 
    }).format(date);
  }
  
  // Calculate days remaining to target weight
  $: daysRemaining = Math.ceil((batch.targetWeight - batch.avgWeight) / (batch.avgWeight / batch.age));
  
  // Format currency values
  function formatCurrency(value) {
    return '$' + value.toFixed(2);
  }
  
  // Format weight values to display in kg when over 1000g
  function formatWeight(value) {
    if (value >= 1000) {
      return (value / 1000).toFixed(1) + 'kg';
    } else {
      return value + 'g';
    }
  }
  
  // Load charts when component mounts
  onMount(() => {
    // In a real app, this would initialize charts using a library like Chart.js
    console.log('Charts would be initialized here');
  });
</script>

<svelte:head>
  <title>{batch.name} Details | BroilerPro</title>
</svelte:head>

<div class="batch-details">
  <!-- Breadcrumb navigation -->
  <div class="breadcrumb">
    <a href="/flock">Flock Management</a>
    <span class="material-icons">chevron_right</span>
    <span>{batch.name}</span>
  </div>
  
  <!-- Header section -->
  <div class="header-section">
    <div class="batch-header">
      <div class="batch-title">
        <h1>{batch.name}</h1>
        <div class="batch-meta">
          <span class="batch-id">{batch.id}</span>
          <span class="status-badge {batch.status}">{batch.status}</span>
        </div>
      </div>
      
      <div class="batch-actions">
        <button class="btn btn-outline-primary">
          <span class="material-icons">edit</span>
          Edit
        </button>
        <button class="btn btn-outline-secondary">
          <span class="material-icons">print</span>
          Print Report
        </button>
        <button class="btn btn-outline-secondary">
          <span class="material-icons">file_download</span>
          Export Data
        </button>
      </div>
    </div>
    
    <div class="key-metrics">
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">calendar_today</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{batch.age} days</h3>
          <p class="metric-label">Current Age</p>
        </div>
      </div>
      
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">pets</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{batch.currentCount.toLocaleString()}</h3>
          <p class="metric-label">Current Birds</p>
        </div>
      </div>
      
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">monitor_weight</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{formatWeight(batch.avgWeight)}</h3>
          <p class="metric-label">Average Weight</p>
        </div>
      </div>
      
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">restaurant</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{batch.feedConversion}</h3>
          <p class="metric-label">FCR</p>
        </div>
      </div>
      
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">trending_down</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{batch.mortality}%</h3>
          <p class="metric-label">Mortality Rate</p>
        </div>
      </div>
      
      <div class="metric-card">
        <div class="metric-icon">
          <span class="material-icons">schedule</span>
        </div>
        <div class="metric-content">
          <h3 class="metric-value">{daysRemaining > 0 ? `${daysRemaining} days` : 'Ready'}</h3>
          <p class="metric-label">{daysRemaining > 0 ? 'To Target Weight' : 'For Slaughter'}</p>
        </div>
      </div>
    </div>
  </div>
  
  <!-- Tab navigation -->
  <div class="tabs-container">
    <div class="tabs">
      {#each tabs as tab}
        <button 
          class="tab-button {activeTab === tab.id ? 'active' : ''}" 
          on:click={() => activeTab = tab.id}
        >
          <span class="material-icons">{tab.icon}</span>
          <span class="tab-label">{tab.label}</span>
        </button>
      {/each}
    </div>
  </div>
  
  <!-- Tab content -->
  <div class="tab-content">
    {#if activeTab === 'overview'}
      <div class="overview-tab">
        <div class="overview-grid">
          <!-- Basic Information Card -->
          <div class="info-card">
            <h3 class="card-title">Basic Information</h3>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">Batch ID</span>
                <span class="info-value">{batch.id}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Start Date</span>
                <span class="info-value">{formatDate(batch.startDate)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Projected Harvest</span>
                <span class="info-value">{formatDate(batch.projectedHarvestDate)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Breed Type</span>
                <span class="info-value">{batch.breedType}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Supplier</span>
                <span class="info-value">{batch.supplier}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Housing</span>
                <span class="info-value">{batch.housing}</span>
              </div>
            </div>
          </div>
          
          <!-- Growth Chart Card -->
          <div class="chart-card">
            <h3 class="card-title">Growth Progress</h3>
            <div class="chart-container" id="growth-chart">
              <div class="chart-placeholder">
                <span class="material-icons">trending_up</span>
                <p>Growth chart will be displayed here</p>
              </div>
            </div>
            <div class="chart-legend">
              <div class="legend-item">
                <span class="legend-color actual"></span>
                <span>Actual Weight</span>
              </div>
              <div class="legend-item">
                <span class="legend-color target"></span>
                <span>Target Weight</span>
              </div>
            </div>
          </div>
          
          <!-- Feed Consumption Chart Card -->
          <div class="chart-card">
            <h3 class="card-title">Feed Consumption</h3>
            <div class="chart-container" id="feed-chart">
              <div class="chart-placeholder">
                <span class="material-icons">restaurant</span>
                <p>Feed consumption chart will be displayed here</p>
              </div>
            </div>
          </div>
          
          <!-- Mortality Chart Card -->
          <div class="chart-card">
            <h3 class="card-title">Mortality Rate</h3>
            <div class="chart-container" id="mortality-chart">
              <div class="chart-placeholder">
                <span class="material-icons">trending_down</span>
                <p>Mortality chart will be displayed here</p>
              </div>
            </div>
          </div>
          
          <!-- Financial Summary Card -->
          <div class="info-card">
            <h3 class="card-title">Financial Summary</h3>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">Total Costs</span>
                <span class="info-value">{formatCurrency(batch.financialData.totalCosts)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Cost per Bird</span>
                <span class="info-value">{formatCurrency(batch.financialData.totalCosts / batch.currentCount)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Feed Cost</span>
                <span class="info-value">{formatCurrency(batch.financialData.costBreakdown.feed)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Projected Revenue</span>
                <span class="info-value">{formatCurrency(batch.financialData.projectedRevenue)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Projected Profit</span>
                <span class="info-value profit">{formatCurrency(batch.financialData.projectedProfit)}</span>
              </div>
              <div class="info-item">
                <span class="info-label">Profit Margin</span>
                <span class="info-value profit">{(batch.financialData.projectedProfit / batch.financialData.projectedRevenue * 100).toFixed(1)}%</span>
              </div>
            </div>
          </div>
          
          <!-- Recent Notes Card -->
          <div class="info-card">
            <h3 class="card-title">Recent Notes</h3>
            <div class="notes-list">
              {#each batch.notes.slice(0, 2) as note}
                <div class="note-item">
                  <div class="note-header">
                    <span class="note-date">{formatDate(note.date)}</span>
                    <span class="note-author">{note.author}</span>
                  </div>
                  <p class="note-content">{note.content}</p>
                </div>
              {/each}
              <a href="#" class="view-all" on:click={() => activeTab = 'documents'}>View all notes</a>
            </div>
          </div>
        </div>
      </div>
    {/if}
    
    {#if activeTab === 'growth'}
      <div class="growth-tab">
        <h2>Growth & Performance</h2>
        <p>Detailed growth and performance data will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
    
    {#if activeTab === 'feed'}
      <div class="feed-tab">
        <h2>Feed Management</h2>
        <p>Detailed feed management data will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
    
    {#if activeTab === 'health'}
      <div class="health-tab">
        <h2>Health & Mortality</h2>
        <p>Detailed health and mortality data will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
    
    {#if activeTab === 'environment'}
      <div class="environment-tab">
        <h2>Environment</h2>
        <p>Detailed environment data will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
    
    {#if activeTab === 'financial'}
      <div class="financial-tab">
        <h2>Financial</h2>
        <p>Detailed financial data will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
    
    {#if activeTab === 'documents'}
      <div class="documents-tab">
        <h2>Documents & Notes</h2>
        <p>Detailed documents and notes will be displayed here.</p>
        <!-- This section will be expanded in the next implementation phase -->
      </div>
    {/if}
  </div>
</div>

<style>
  .batch-details {
    padding: 16px;
  }
  
  .breadcrumb {
    display: flex;
    align-items: center;
    margin-bottom: 16px;
    font-size: 14px;
  }
  
  .breadcrumb a {
    color: var(--primary);
    text-decoration: none;
  }
  
  .breadcrumb .material-icons {
    font-size: 16px;
    margin: 0 8px;
    color: var(--gray);
  }
  
  .header-section {
    margin-bottom: 24px;
  }
  
  .batch-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 16px;
  }
  
  .batch-title h1 {
    margin: 0;
    font-size: 28px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .batch-meta {
    display: flex;
    align-items: center;
    margin-top: 4px;
  }
  
  .batch-id {
    font-size: 14px;
    color: var(--gray);
    margin-right: 12px;
  }
  
  .status-badge {
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
    text-transform: capitalize;
  }
  
  .status-badge.healthy {
    background-color: var(--success-light);
    color: var(--success);
  }
  
  .status-badge.warning {
    background-color: var(--warning-light);
    color: var(--warning);
  }
  
  .status-badge.danger {
    background-color: var(--danger-light);
    color: var(--danger);
  }
  
  .batch-actions {
    display: flex;
    gap: 8px;
  }
  
  .key-metrics {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    gap: 16px;
  }
  
  .metric-card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    display: flex;
    align-items: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .metric-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .metric-icon {
    width: 48px;
    height: 48px;
    border-radius: 8px;
    background-color: rgba(25, 118, 210, 0.1);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .metric-icon .material-icons {
    font-size: 24px;
    color: var(--primary);
  }
  
  .metric-content {
    flex: 1;
  }
  
  .metric-value {
    font-size: 20px;
    font-weight: 500;
    margin: 0 0 4px;
    color: var(--dark);
  }
  
  .metric-label {
    font-size: 12px;
    color: var(--gray);
    margin: 0;
  }
  
  .tabs-container {
    margin-bottom: 24px;
  }
  
  .tabs {
    display: flex;
    border-bottom: 1px solid var(--gray-light);
    overflow-x: auto;
    scrollbar-width: none;
  }
  
  .tabs::-webkit-scrollbar {
    display: none;
  }
  
  .tab-button {
    padding: 12px 16px;
    background: none;
    border: none;
    border-bottom: 2px solid transparent;
    cursor: pointer;
    white-space: nowrap;
    display: flex;
    align-items: center;
    gap: 8px;
    color: var(--gray);
    transition: all 0.2s ease;
  }
  
  .tab-button:hover {
    color: var(--primary);
  }
  
  .tab-button.active {
    color: var(--primary);
    border-bottom-color: var(--primary);
  }
  
  .tab-button .material-icons {
    font-size: 18px;
  }
  
  .tab-content {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 24px;
  }
  
  .overview-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
  
  .info-card, .chart-card {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
  }
  
  .card-title {
    margin: 0 0 16px;
    font-size: 16px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .info-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 12px;
  }
  
  .info-item {
    display: flex;
    flex-direction: column;
  }
  
  .info-label {
    font-size: 12px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .info-value {
    font-size: 14px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .info-value.profit {
    color: var(--success);
  }
  
  .chart-container {
    height: 200px;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: white;
    border-radius: 4px;
    margin-bottom: 16px;
  }
  
  .chart-placeholder {
    display: flex;
    flex-direction: column;
    align-items: center;
    color: var(--gray-light);
  }
  
  .chart-placeholder .material-icons {
    font-size: 48px;
    margin-bottom: 8px;
  }
  
  .chart-legend {
    display: flex;
    gap: 16px;
    justify-content: center;
  }
  
  .legend-item {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 12px;
    color: var(--gray);
  }
  
  .legend-color {
    width: 12px;
    height: 12px;
    border-radius: 2px;
  }
  
  .legend-color.actual {
    background-color: var(--primary);
  }
  
  .legend-color.target {
    background-color: var(--gray-light);
    border: 1px dashed var(--gray);
  }
  
  .notes-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }
  
  .note-item {
    padding: 12px;
    background-color: white;
    border-radius: 4px;
    border-left: 3px solid var(--primary-light);
  }
  
  .note-header {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
    font-size: 12px;
  }
  
  .note-date {
    color: var(--gray);
  }
  
  .note-author {
    font-weight: 500;
    color: var(--dark);
  }
  
  .note-content {
    margin: 0;
    font-size: 13px;
    color: var(--dark);
  }
  
  .view-all {
    font-size: 13px;
    color: var(--primary);
    text-decoration: none;
    text-align: center;
    padding: 8px;
  }
  
  @media (max-width: 992px) {
    .key-metrics {
      grid-template-columns: repeat(3, 1fr);
    }
    
    .overview-grid {
      grid-template-columns: 1fr;
    }
  }
  
  @media (max-width: 768px) {
    .batch-header {
      flex-direction: column;
      gap: 16px;
    }
    
    .batch-actions {
      width: 100%;
    }
    
    .key-metrics {
      grid-template-columns: repeat(2, 1fr);
    }
    
    .tab-label {
      display: none;
    }
    
    .tab-button {
      flex: 1;
      justify-content: center;
    }
  }
  
  @media (max-width: 576px) {
    .key-metrics {
      grid-template-columns: 1fr;
    }
    
    .info-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
