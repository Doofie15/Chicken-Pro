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
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 16px;
    position: relative;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
    transition: transform 0.2s, box-shadow 0.2s;
  }
  
  .flock-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.12);
  }
  
  .flock-card:last-child {
    margin-bottom: 0;
  }
  
  .flock-card.warning {
    border-left: 4px solid var(--warning);
  }
  
  .flock-card.danger {
    border-left: 4px solid var(--danger);
  }
  

  
  .flock-content {
    flex: 1;
    padding: 16px 20px;
    position: relative;
    background-color: white;
    border-radius: 8px;
  }
  
  .flock-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }
  
  .flock-name {
    margin: 0;
    font-size: 20px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .flock-id {
    margin: 4px 0 0;
    font-size: 13px;
    color: var(--gray);
    font-weight: 500;
  }
  
  .flock-age {
    background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 100%);
    color: white;
    border-radius: 6px;
    padding: 8px 12px;
    text-align: center;
    min-width: 60px;
    box-shadow: 0 2px 4px rgba(25, 118, 210, 0.2);
  }
  
  .age-value {
    font-size: 22px;
    font-weight: 700;
    display: block;
    line-height: 1;
  }
  
  .age-label {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    margin-top: 2px;
    font-weight: 500;
  }
  
  .flock-progress {
    margin-bottom: 20px;
  }
  
  .progress-info {
    display: flex;
    justify-content: space-between;
    margin-bottom: 6px;
    font-size: 13px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .progress-bar {
    height: 8px;
    background-color: #f0f0f0;
    border-radius: 4px;
    overflow: hidden;
    box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.05);
  }
  
  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--primary) 0%, var(--primary-light) 100%);
    border-radius: 4px;
    transition: width 0.5s ease;
  }
  
  .flock-stats {
    display: flex;
    margin-bottom: 20px;
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 12px;
  }
  
  .stat {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    padding: 0 8px;
    position: relative;
  }
  
  .stat:not(:last-child):after {
    content: '';
    position: absolute;
    right: 0;
    top: 10%;
    height: 80%;
    width: 1px;
    background-color: #e0e0e0;
  }
  
  .stat-label {
    font-size: 12px;
    font-weight: 600;
    color: var(--gray);
    text-transform: uppercase;
    letter-spacing: 0.5px;
    margin-bottom: 6px;
  }
  
  .stat-value {
    font-size: 18px;
    font-weight: 600;
    color: var(--dark);
    margin-bottom: 4px;
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
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 13px;
    font-weight: 500;
    color: var(--gray);
    margin-top: 12px;
    background-color: #f5f5f5;
    padding: 10px;
    border-radius: 6px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
  }
  
  .flock-forecast.ready {
    color: white;
    background: linear-gradient(90deg, var(--success) 0%, var(--success-light) 100%);
  }
  
  .flock-forecast .material-icons {
    font-size: 16px;
    margin-right: 6px;
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
