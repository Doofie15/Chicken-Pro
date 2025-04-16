<script>
  export let flock;
  
  // Calculate days remaining to target weight
  $: daysRemaining = Math.ceil((flock.targetWeight - flock.avgWeight) / (flock.avgWeight / flock.age));
  
  // Format date for display
  function formatDate(dateString) {
    const date = new Date(dateString);
    return new Intl.DateTimeFormat('en-US', { 
      month: 'short', 
      day: 'numeric', 
      year: 'numeric' 
    }).format(date);
  }
</script>

<div class="flock-card" class:warning={flock.status === 'warning'} class:danger={flock.status === 'danger'}>
  <div class="flock-image" style="background-image: url({flock.image})">
    <div class="flock-badge">
      <span class="material-icons">
        {#if flock.status === 'healthy'}
          check_circle
        {:else if flock.status === 'warning'}
          warning
        {:else}
          error
        {/if}
      </span>
      {flock.status}
    </div>
  </div>
  
  <div class="flock-content">
    <div class="flock-header">
      <div>
        <h3 class="flock-name">{flock.name}</h3>
        <p class="flock-id">{flock.id}</p>
      </div>
      <div class="flock-age">
        <span class="age-value">{flock.age}</span>
        <span class="age-label">days</span>
      </div>
    </div>
    
    <div class="flock-progress">
      <div class="progress-info">
        <span>Growth Progress</span>
        <span>{flock.progress}%</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" style="width: {flock.progress}%"></div>
      </div>
    </div>
    
    <div class="flock-stats">
      <div class="stat">
        <span class="stat-label">Birds</span>
        <span class="stat-value">{flock.currentCount.toLocaleString()}</span>
        <span class="stat-secondary">of {flock.initialCount.toLocaleString()}</span>
      </div>
      
      <div class="stat">
        <span class="stat-label">Weight</span>
        <span class="stat-value">{flock.avgWeight}g</span>
        <span class="stat-secondary">target: {flock.targetWeight}g</span>
      </div>
      
      <div class="stat">
        <span class="stat-label">FCR</span>
        <span class="stat-value">{flock.feedConversion}</span>
        <span class="stat-secondary">
          {flock.feedConversion < 1.7 ? 'Excellent' : flock.feedConversion < 1.8 ? 'Good' : 'Fair'}
        </span>
      </div>
    </div>
    
    <div class="flock-footer">
      <div class="flock-date">
        <span class="material-icons">calendar_today</span>
        Started: {formatDate(flock.startDate)}
      </div>
      
      <div class="flock-actions">
        <button class="btn-icon">
          <span class="material-icons">edit</span>
        </button>
        <button class="btn-icon">
          <span class="material-icons">visibility</span>
        </button>
        <button class="btn-icon">
          <span class="material-icons">more_vert</span>
        </button>
      </div>
    </div>
    
    {#if daysRemaining > 0}
      <div class="flock-forecast">
        <span class="material-icons">schedule</span>
        Estimated {daysRemaining} days to target weight
      </div>
    {:else}
      <div class="flock-forecast ready">
        <span class="material-icons">check_circle</span>
        Ready for harvest
      </div>
    {/if}
  </div>
</div>

<style>
  .flock-card {
    display: flex;
    background-color: white;
    border-radius: 0;
    overflow: hidden;
    border-bottom: 1px solid var(--gray-light);
    position: relative;
  }
  
  .flock-card:last-child {
    border-bottom: none;
  }
  
  .flock-card.warning {
    background-color: rgba(255, 152, 0, 0.05);
  }
  
  .flock-card.danger {
    background-color: rgba(244, 67, 54, 0.05);
  }
  
  .flock-image {
    width: 120px;
    background-size: cover;
    background-position: center;
    position: relative;
  }
  
  .flock-badge {
    position: absolute;
    top: 8px;
    left: 8px;
    background-color: rgba(0, 0, 0, 0.6);
    color: white;
    font-size: 12px;
    padding: 4px 8px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    text-transform: capitalize;
  }
  
  .flock-badge .material-icons {
    font-size: 14px;
    margin-right: 4px;
  }
  
  .flock-content {
    flex: 1;
    padding: 16px;
    position: relative;
  }
  
  .flock-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 12px;
  }
  
  .flock-name {
    margin: 0;
    font-size: 18px;
    font-weight: 500;
  }
  
  .flock-id {
    margin: 4px 0 0;
    font-size: 12px;
    color: var(--gray);
  }
  
  .flock-age {
    background-color: var(--primary);
    color: white;
    border-radius: 4px;
    padding: 4px 8px;
    text-align: center;
    min-width: 60px;
  }
  
  .age-value {
    font-size: 18px;
    font-weight: 500;
    display: block;
  }
  
  .age-label {
    font-size: 12px;
    text-transform: uppercase;
  }
  
  .flock-progress {
    margin-bottom: 16px;
  }
  
  .progress-info {
    display: flex;
    justify-content: space-between;
    font-size: 12px;
    margin-bottom: 4px;
  }
  
  .progress-bar {
    height: 6px;
    background-color: var(--gray-light);
    border-radius: 3px;
    overflow: hidden;
  }
  
  .progress-fill {
    height: 100%;
    background-color: var(--primary);
    border-radius: 3px;
  }
  
  .flock-stats {
    display: flex;
    margin-bottom: 16px;
  }
  
  .stat {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  
  .stat-label {
    font-size: 12px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .stat-value {
    font-size: 16px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .stat-secondary {
    font-size: 12px;
    color: var(--gray);
  }
  
  .flock-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .flock-date {
    display: flex;
    align-items: center;
    font-size: 12px;
    color: var(--gray);
  }
  
  .flock-date .material-icons {
    font-size: 14px;
    margin-right: 4px;
  }
  
  .flock-actions {
    display: flex;
    gap: 4px;
  }
  
  .btn-icon {
    width: 32px;
    height: 32px;
    border-radius: 50%;
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
  
  .flock-forecast {
    position: absolute;
    bottom: 16px;
    right: 16px;
    font-size: 12px;
    color: var(--primary);
    display: flex;
    align-items: center;
  }
  
  .flock-forecast.ready {
    color: var(--success);
  }
  
  .flock-forecast .material-icons {
    font-size: 14px;
    margin-right: 4px;
  }
  
  @media (max-width: 576px) {
    .flock-card {
      flex-direction: column;
    }
    
    .flock-image {
      width: 100%;
      height: 120px;
    }
    
    .flock-footer {
      margin-bottom: 24px;
    }
    
    .flock-forecast {
      position: relative;
      bottom: auto;
      right: auto;
      margin-top: 12px;
    }
  }
</style>
