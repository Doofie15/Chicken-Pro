<script>
  export let flock;
  
  // Calculate days remaining to target weight
  $: daysRemaining = Math.ceil((flock.targetWeight - flock.avgWeight) / (flock.avgWeight / flock.age));
  
  // Sample feed data - in a real app, this would come from the flock object
  $: feedData = {
    totalFeed: 12500 + (flock.age * 150), // Sample calculation in grams
    dailyFeed: 250 + (flock.age * 0.5), // Sample calculation
    feedType: flock.age < 21 ? 'Starter' : flock.age < 42 ? 'Grower' : 'Finisher',
    feedCost: (12500 + (flock.age * 150)) * 0.45 / 1000, // Cost in currency units (per kg)
    costPerBird: ((12500 + (flock.age * 150)) * 0.45 / 1000) / flock.currentCount
  };
  
  /**
   * Format weight values to display in kg when over 1000g
   * @param {number} value - The weight value in grams
   * @returns {string} Formatted weight string
   */
  function formatWeight(value) {
    if (value >= 1000) {
      return (value / 1000).toFixed(1) + 'kg';
    } else {
      return value + 'g';
    }
  }
  
  /**
   * Format currency values
   * @param {number} value - The currency value
   * @returns {string} Formatted currency string
   */
  function formatCurrency(value) {
    return '$' + value.toFixed(2);
  }
  
  /**
   * Format date for display
   * @param {string} dateString - The date string to format
   * @returns {string} Formatted date string
   */
  function formatDate(dateString) {
    const date = new Date(dateString);
    return new Intl.DateTimeFormat('en-US', { 
      month: 'short', 
      day: 'numeric', 
      year: 'numeric' 
    }).format(date);
  }
</script>

<div class="flock-row" class:warning={flock.status === 'warning'} class:danger={flock.status === 'danger'}>
  <!-- Batch info column -->
  <div class="flock-cell batch-info">
    <div class="batch-name-id">
      <h3 class="batch-name">{flock.name}</h3>
      <span class="batch-id">{flock.id}</span>
    </div>
  </div>
  
  <!-- Age column -->
  <div class="flock-cell age-cell">
    <div class="age-badge">
      <span class="age-value">{flock.age}</span>
      <span class="age-label">days</span>
    </div>
  </div>
  
  <!-- Progress column -->
  <div class="flock-cell progress-cell">
    <div class="progress-container">
      <div class="progress-bar">
        <div class="progress-fill" style="width: {flock.progress}%"></div>
      </div>
      <div class="progress-value">{flock.progress}%</div>
    </div>
  </div>
  
  <!-- Birds column -->
  <div class="flock-cell metric-cell">
    <span class="metric-value">{flock.currentCount.toLocaleString()}</span>
    <span class="metric-label">Birds</span>
  </div>
  
  <!-- Weight column -->
  <div class="flock-cell metric-cell">
    <span class="metric-value">{formatWeight(flock.avgWeight)}</span>
    <span class="metric-label">Weight</span>
  </div>
  
  <!-- FCR column -->
  <div class="flock-cell metric-cell">
    <span class="metric-value">{flock.feedConversion}</span>
    <span class="metric-label">FCR</span>
  </div>
  
  <!-- Mortality column -->
  <div class="flock-cell metric-cell">
    <span class="metric-value">{flock.mortality}%</span>
    <span class="metric-label">Mortality</span>
  </div>
  
  <!-- Feed column -->
  <div class="flock-cell feed-cell">
    <div class="feed-cards">
      <div class="feed-card">
        <span class="feed-value">{(feedData.totalFeed / 1000).toFixed(1)}kg</span>
        <span class="feed-label">Total Feed</span>
      </div>
      <div class="feed-card">
        <span class="feed-value">{formatCurrency(feedData.feedCost)}</span>
        <span class="feed-label">Feed Cost</span>
      </div>
      <div class="feed-card">
        <span class="feed-value">{formatCurrency(feedData.costPerBird)}</span>
        <span class="feed-label">Cost/Bird</span>
      </div>
    </div>
  </div>
  
  <!-- Forecast column -->
  <div class="flock-cell forecast-cell">
    {#if daysRemaining > 0}
      <div class="forecast-badge">
        <span class="material-icons">schedule</span>
        <span>{daysRemaining}d</span>
      </div>
    {:else}
      <div class="ready-status">
        <div class="ready-pulse"></div>
        <span class="ready-text">Ready for Slaughter</span>
      </div>
    {/if}
  </div>
  
  <!-- Actions column -->
  <div class="flock-cell actions-cell">
    <div class="action-buttons">
      <button class="btn-icon" title="Edit">
        <span class="material-icons">edit</span>
      </button>
      <button class="btn-icon" title="View Details">
        <span class="material-icons">visibility</span>
      </button>
      <button class="btn-icon" title="More Options">
        <span class="material-icons">more_vert</span>
      </button>
    </div>
  </div>
</div>

<style>
  .flock-row {
    display: flex;
    align-items: center;
    background-color: white;
    border-radius: 4px;
    margin-bottom: 8px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
    transition: transform 0.15s, box-shadow 0.15s;
    height: 60px;
    position: relative;
    width: 100%;
  }
  
  .flock-row:hover {
    transform: translateY(-1px);
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    background-color: #fafafa;
  }
  
  .flock-row.warning {
    border-left: 3px solid var(--warning);
  }
  
  .flock-row.danger {
    border-left: 3px solid var(--danger);
  }
  
  .flock-cell {
    padding: 0 10px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;
  }
  
  .batch-info {
    flex: 0 0 160px;
    padding-left: 16px;
  }
  
  .batch-name-id {
    display: flex;
    flex-direction: column;
  }
  
  .batch-name {
    margin: 0;
    font-size: 14px;
    font-weight: 600;
    color: var(--dark);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  
  .batch-id {
    font-size: 11px;
    color: var(--gray);
    font-weight: 500;
  }
  
  .age-cell {
    flex: 0 0 60px;
  }
  
  .age-badge {
    background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 100%);
    color: white;
    border-radius: 4px;
    padding: 4px 8px;
    text-align: center;
    display: inline-flex;
    flex-direction: column;
    align-items: center;
    min-width: 45px;
  }
  
  .age-value {
    font-size: 16px;
    font-weight: 700;
    line-height: 1;
  }
  
  .age-label {
    font-size: 9px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    margin-top: 2px;
    font-weight: 500;
  }
  
  .progress-cell {
    flex: 0 0 100px;
  }
  
  .progress-container {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
  
  .progress-bar {
    height: 5px;
    background-color: #f0f0f0;
    border-radius: 3px;
    overflow: hidden;
    width: 100%;
  }
  
  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--primary) 0%, var(--primary-light) 100%);
    border-radius: 3px;
    transition: width 0.5s ease;
  }
  
  .progress-value {
    font-size: 11px;
    font-weight: 600;
    color: var(--dark);
    text-align: right;
  }
  
  .metric-cell {
    flex: 1 1 80px;
    text-align: center;
  }
  
  .metric-value {
    font-size: 14px;
    font-weight: 600;
    color: var(--dark);
    display: block;
  }
  
  .metric-label {
    font-size: 10px;
    font-weight: 500;
    color: var(--gray);
    text-transform: uppercase;
    letter-spacing: 0.3px;
  }
  
  .feed-cell {
    flex: 1 1 180px;
    text-align: left;
  }
  
  .feed-cards {
    display: flex;
    gap: 6px;
  }
  
  .feed-card {
    display: flex;
    flex-direction: column;
    background-color: #f8f9fa;
    border-radius: 4px;
    padding: 4px 8px;
    min-width: 55px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
    text-align: center;
  }
  
  .feed-value {
    font-size: 13px;
    font-weight: 600;
    color: var(--dark);
    line-height: 1.2;
  }
  
  .feed-label {
    font-size: 9px;
    font-weight: 500;
    color: var(--primary);
    text-transform: uppercase;
    letter-spacing: 0.3px;
  }
  
  .forecast-cell {
    flex: 0 0 90px;
    text-align: center;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .forecast-badge {
    display: inline-flex;
    align-items: center;
    background-color: #f0f0f0;
    border-radius: 4px;
    padding: 4px 8px;
    gap: 4px;
  }
  
  .forecast-badge .material-icons {
    font-size: 14px;
  }
  
  .ready-status {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .ready-pulse {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background-color: var(--success);
    position: relative;
    margin-bottom: 4px;
  }
  
  .ready-pulse::before {
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color: var(--success);
    opacity: 0.7;
    animation: pulse 2s infinite;
    top: 0;
    left: 0;
  }
  
  .ready-text {
    font-size: 10px;
    font-weight: 600;
    color: var(--success);
    white-space: nowrap;
  }
  
  @keyframes pulse {
    0% {
      transform: scale(1);
      opacity: 0.7;
    }
    70% {
      transform: scale(2);
      opacity: 0;
    }
    100% {
      transform: scale(1);
      opacity: 0;
    }
  }
  
  .actions-cell {
    flex: 0 0 90px;
  }
  
  .action-buttons {
    display: flex;
    gap: 2px;
    justify-content: center;
  }
  
  .btn-icon {
    width: 28px;
    height: 28px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--gray);
    transition: background-color 0.2s ease;
  }
  
  .btn-icon:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
  
  .btn-icon .material-icons {
    font-size: 16px;
  }
  
  @media (max-width: 992px) {
    .flock-row {
      flex-wrap: wrap;
      height: auto;
      padding: 10px 0;
      width: 100%;
    }
    
    .batch-info {
      flex: 1 0 50%;
      order: 1;
    }
    
    .age-cell {
      flex: 0 0 auto;
      order: 2;
    }
    
    .actions-cell {
      flex: 0 0 auto;
      order: 3;
      margin-left: auto;
    }
    
    .progress-cell {
      flex: 1 0 100%;
      order: 4;
      padding-top: 8px;
      padding-bottom: 8px;
    }
    
    .metric-cell {
      flex: 1 0 25%;
      order: 5;
    }
    
    .feed-cell {
      flex: 1 0 100%;
      order: 6;
      text-align: center;
      padding-top: 8px;
      padding-bottom: 8px;
    }
    
    .feed-cards {
      justify-content: center;
    }
    
    .forecast-cell {
      flex: 1 0 100%;
      order: 7;
      text-align: center;
      padding-top: 8px;
      padding-bottom: 8px;
    }
    
    .ready-status {
      flex-direction: row;
      gap: 8px;
      justify-content: center;
    }
    
    .ready-pulse {
      margin-bottom: 0;
    }
  }
  
  @media (max-width: 576px) {
    .metric-cell {
      flex: 1 0 50%;
      padding: 6px 10px;
    }
    
    .feed-cell {
      flex: 1 0 100%;
      padding: 6px 10px;
    }
    
    .feed-cards {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
    }
    
    .feed-card {
      width: 100%;
    }
  }
</style>
