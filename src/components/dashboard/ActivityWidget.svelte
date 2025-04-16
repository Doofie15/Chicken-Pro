<script>
  export let activities = [];
</script>

<div class="activity-widget">
  {#each activities as activity, index}
    <div class="activity-item">
      <div class="activity-timeline">
        <div class="activity-dot" style="background-color: {activity.iconColor || 'var(--primary)'}"></div>
        {#if index < activities.length - 1}
          <div class="activity-line"></div>
        {/if}
      </div>
      
      <div class="activity-icon">
        <span class="material-icons" style="color: {activity.iconColor || 'var(--primary)'}">
          {activity.icon}
        </span>
      </div>
      
      <div class="activity-content">
        <div class="activity-header">
          <span class="activity-user">{activity.user}</span>
          <span class="activity-action">{activity.action}</span>
        </div>
        <div class="activity-description">
          {activity.description}
        </div>
        <div class="activity-time">
          {activity.time}
        </div>
      </div>
    </div>
  {/each}
</div>

<style>
  .activity-widget {
    width: 100%;
  }
  
  .activity-item {
    display: flex;
    padding: 16px;
    position: relative;
  }
  
  .activity-timeline {
    position: absolute;
    left: 16px;
    top: 0;
    bottom: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 2px;
  }
  
  .activity-dot {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    margin-top: 22px;
    z-index: 2;
  }
  
  .activity-line {
    flex: 1;
    width: 2px;
    background-color: var(--gray-light);
    margin-top: 4px;
  }
  
  .activity-icon {
    width: 36px;
    height: 36px;
    border-radius: 8px;
    background-color: rgba(25, 118, 210, 0.1);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
    margin-left: 20px;
  }
  
  .activity-icon .material-icons {
    font-size: 20px;
  }
  
  .activity-content {
    flex: 1;
  }
  
  .activity-header {
    margin-bottom: 4px;
  }
  
  .activity-user {
    font-weight: 500;
    color: var(--dark);
  }
  
  .activity-action {
    color: var(--dark);
  }
  
  .activity-description {
    color: var(--gray);
    font-size: 14px;
    margin-bottom: 4px;
  }
  
  .activity-time {
    color: var(--gray);
    font-size: 12px;
  }
</style>
