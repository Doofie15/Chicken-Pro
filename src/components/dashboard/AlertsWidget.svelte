<script>
  export let alerts = [];
  
  function getAlertIcon(type) {
    if (type === 'warning') return 'warning';
    if (type === 'danger') return 'error';
    if (type === 'success') return 'check_circle';
    return 'info';
  }
  
  function dismissAlert(id) {
    alerts = alerts.filter(alert => alert.id !== id);
  }
</script>

<div class="alerts-widget">
  {#if alerts.length === 0}
    <div class="no-alerts">
      <span class="material-icons">notifications_off</span>
      <p>No new alerts</p>
    </div>
  {:else}
    {#each alerts as alert}
      <div class="alert-item alert-{alert.type}">
        <div class="alert-icon">
          <span class="material-icons">{getAlertIcon(alert.type)}</span>
        </div>
        
        <div class="alert-content">
          <p class="alert-message">{alert.message}</p>
          <p class="alert-time">{alert.time}</p>
        </div>
        
        <button class="alert-dismiss" on:click={() => dismissAlert(alert.id)}>
          <span class="material-icons">close</span>
        </button>
      </div>
    {/each}
    
    <div class="alerts-actions">
      <button class="btn-text">Mark all as read</button>
      <button class="btn-text">View all alerts</button>
    </div>
  {/if}
</div>

<style>
  .alerts-widget {
    width: 100%;
  }
  
  .no-alerts {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 32px 16px;
    color: var(--gray);
  }
  
  .no-alerts .material-icons {
    font-size: 48px;
    margin-bottom: 16px;
    opacity: 0.5;
  }
  
  .no-alerts p {
    margin: 0;
    font-size: 14px;
  }
  
  .alert-item {
    display: flex;
    align-items: flex-start;
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
    transition: background-color 0.2s ease;
  }
  
  .alert-item:hover {
    background-color: rgba(0, 0, 0, 0.02);
  }
  
  .alert-warning {
    border-left: 3px solid var(--warning);
  }
  
  .alert-danger {
    border-left: 3px solid var(--danger);
  }
  
  .alert-info {
    border-left: 3px solid var(--info);
  }
  
  .alert-success {
    border-left: 3px solid var(--success);
  }
  
  .alert-icon {
    margin-right: 12px;
  }
  
  .alert-warning .alert-icon {
    color: var(--warning);
  }
  
  .alert-danger .alert-icon {
    color: var(--danger);
  }
  
  .alert-info .alert-icon {
    color: var(--info);
  }
  
  .alert-success .alert-icon {
    color: var(--success);
  }
  
  .alert-content {
    flex: 1;
  }
  
  .alert-message {
    margin: 0 0 4px;
    font-size: 14px;
    color: var(--dark);
  }
  
  .alert-time {
    margin: 0;
    font-size: 12px;
    color: var(--gray);
  }
  
  .alert-dismiss {
    background: none;
    border: none;
    color: var(--gray);
    cursor: pointer;
    padding: 4px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.2s ease;
  }
  
  .alert-dismiss:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
  
  .alert-dismiss .material-icons {
    font-size: 16px;
  }
  
  .alerts-actions {
    display: flex;
    justify-content: space-between;
    padding: 12px 16px;
    border-top: 1px solid var(--gray-light);
  }
  
  .btn-text {
    background: none;
    border: none;
    color: var(--primary);
    font-size: 12px;
    font-weight: 500;
    cursor: pointer;
    padding: 0;
  }
  
  .btn-text:hover {
    text-decoration: underline;
  }
</style>
