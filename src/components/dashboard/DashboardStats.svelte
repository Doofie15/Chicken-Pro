<script>
  export let stats = [];
</script>

<div class="stats-container">
  {#each stats as stat}
    <div class="stat-card bg-white">
      <div class="stat-icon-container bg-{stat.color}-light">
        <span class="material-icons stat-icon text-{stat.color}">{stat.icon}</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-title">{stat.title}</h3>
        <div class="stat-value-container">
          <span class="stat-value">{stat.value}</span>
          <span class="stat-change trend-{stat.trend}">
            {stat.change}
            <span class="material-icons trend-icon">
              {stat.trend === 'up' ? 'arrow_upward' : 'arrow_downward'}
            </span>
          </span>
        </div>
      </div>
    </div>
  {/each}
</div>

<style>
  .stats-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
  }
  
  .stat-card {
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    display: flex;
    align-items: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .stat-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .stat-icon-container {
    width: 48px;
    height: 48px;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .bg-primary-light {
    background-color: rgba(25, 118, 210, 0.1);
  }
  
  .bg-success-light {
    background-color: rgba(76, 175, 80, 0.1);
  }
  
  .bg-warning-light {
    background-color: rgba(255, 152, 0, 0.1);
  }
  
  .bg-danger-light {
    background-color: rgba(244, 67, 54, 0.1);
  }
  
  .bg-info-light {
    background-color: rgba(33, 150, 243, 0.1);
  }
  
  .stat-icon {
    font-size: 24px;
  }
  
  .text-primary {
    color: var(--primary);
  }
  
  .text-success {
    color: var(--success);
  }
  
  .text-warning {
    color: var(--warning);
  }
  
  .text-danger {
    color: var(--danger);
  }
  
  .text-info {
    color: var(--info);
  }
  
  .stat-content {
    flex: 1;
  }
  
  .stat-title {
    font-size: 14px;
    color: var(--gray);
    margin: 0 0 4px;
    font-weight: 400;
  }
  
  .stat-value-container {
    display: flex;
    align-items: baseline;
  }
  
  .stat-value {
    font-size: 24px;
    font-weight: 500;
    color: var(--dark);
    margin-right: 8px;
  }
  
  .stat-change {
    font-size: 12px;
    font-weight: 500;
    display: flex;
    align-items: center;
  }
  
  .trend-up {
    color: var(--success);
  }
  
  .trend-down {
    color: var(--danger);
  }
  
  .trend-icon {
    font-size: 14px;
    margin-left: 2px;
  }
  
  @media (max-width: 992px) {
    .stats-container {
      grid-template-columns: repeat(2, 1fr);
    }
  }
  
  @media (max-width: 576px) {
    .stats-container {
      grid-template-columns: 1fr;
    }
  }
</style>
