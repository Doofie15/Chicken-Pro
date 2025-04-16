<script>
  // Sample mortality data - in a real app, this would come from an API
  export let mortalityData = {
    totalMortality: 312,
    mortalityRate: 2.3,
    weeklyTrend: [1.2, 1.5, 2.0, 2.3, 2.5, 2.3, 2.3],
    batchMortality: [
      {
        id: 'B-2023-45',
        name: 'Batch 45',
        count: 130,
        rate: 2.0,
        trend: 'stable'
      },
      {
        id: 'B-2023-44',
        name: 'Batch 44',
        count: 150,
        rate: 2.5,
        trend: 'up'
      },
      {
        id: 'B-2023-43',
        name: 'Batch 43',
        count: 180,
        rate: 2.9,
        trend: 'down'
      }
    ],
    recentEvents: [
      {
        date: '2025-04-15',
        batch: 'Batch 45',
        count: 12,
        cause: 'Heat stress'
      },
      {
        date: '2025-04-14',
        batch: 'Batch 44',
        count: 8,
        cause: 'Natural causes'
      },
      {
        date: '2025-04-13',
        batch: 'Batch 45',
        count: 5,
        cause: 'Natural causes'
      }
    ]
  };
</script>

<div class="mortality-widget">
  <div class="mortality-summary">
    <div class="summary-item">
      <div class="summary-value">{mortalityData.totalMortality}</div>
      <div class="summary-label">Total Birds Lost</div>
    </div>
    <div class="summary-item">
      <div class="summary-value">{mortalityData.mortalityRate}%</div>
      <div class="summary-label">Average Mortality Rate</div>
    </div>
  </div>
  
  <div class="mortality-trend">
    <h3 class="section-title">Weekly Trend</h3>
    <div class="trend-chart">
      {#each mortalityData.weeklyTrend as rate, i}
        <div class="trend-bar" style="height: {rate * 15}px" title="{rate}%"></div>
      {/each}
    </div>
    <div class="trend-labels">
      {#each Array(7) as _, i}
        <div class="trend-label">W{i+1}</div>
      {/each}
    </div>
  </div>
  
  <div class="batch-mortality">
    <h3 class="section-title">Mortality by Batch</h3>
    {#each mortalityData.batchMortality as batch}
      <div class="batch-item">
        <div class="batch-name">{batch.name}</div>
        <div class="batch-stats">
          <div class="batch-count">{batch.count} birds</div>
          <div class="batch-rate {batch.trend}">{batch.rate}%
            <span class="material-icons">
              {#if batch.trend === 'up'}
                trending_up
              {:else if batch.trend === 'down'}
                trending_down
              {:else}
                trending_flat
              {/if}
            </span>
          </div>
        </div>
      </div>
    {/each}
  </div>
  
  <div class="recent-events">
    <h3 class="section-title">Recent Events</h3>
    {#each mortalityData.recentEvents as event}
      <div class="event-item">
        <div class="event-date">{new Date(event.date).toLocaleDateString('en-US', {month: 'short', day: 'numeric'})}</div>
        <div class="event-details">
          <div class="event-batch">{event.batch}</div>
          <div class="event-cause">{event.cause}</div>
        </div>
        <div class="event-count">-{event.count}</div>
      </div>
    {/each}
  </div>
</div>

<style>
  .mortality-widget {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  .mortality-summary {
    display: flex;
    gap: 12px;
  }
  
  .summary-item {
    flex: 1;
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 12px;
    text-align: center;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .summary-value {
    font-size: 24px;
    font-weight: 600;
    color: var(--dark);
    margin-bottom: 4px;
  }
  
  .summary-label {
    font-size: 13px;
    color: var(--gray);
  }
  
  .section-title {
    font-size: 14px;
    font-weight: 600;
    margin: 0 0 12px 0;
    color: var(--dark);
  }
  
  .mortality-trend {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 12px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .trend-chart {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    height: 40px;
    margin-bottom: 4px;
  }
  
  .trend-bar {
    width: 12px;
    background: linear-gradient(to top, #ff9800, #f44336);
    border-radius: 2px;
  }
  
  .trend-labels {
    display: flex;
    justify-content: space-between;
  }
  
  .trend-label {
    font-size: 10px;
    color: var(--gray);
    text-align: center;
    width: 12px;
  }
  
  .batch-mortality {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 12px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .batch-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 0;
    border-bottom: 1px solid #eee;
  }
  
  .batch-item:last-child {
    border-bottom: none;
  }
  
  .batch-name {
    font-weight: 500;
    font-size: 13px;
  }
  
  .batch-stats {
    display: flex;
    align-items: center;
    gap: 12px;
  }
  
  .batch-count {
    font-size: 12px;
    color: var(--gray);
  }
  
  .batch-rate {
    font-size: 13px;
    font-weight: 500;
    display: flex;
    align-items: center;
  }
  
  .batch-rate.up {
    color: var(--danger);
  }
  
  .batch-rate.down {
    color: var(--success);
  }
  
  .batch-rate.stable {
    color: var(--gray);
  }
  
  .batch-rate .material-icons {
    font-size: 16px;
    margin-left: 2px;
  }
  
  .recent-events {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 12px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .event-item {
    display: flex;
    align-items: center;
    padding: 6px 0;
    border-bottom: 1px solid #eee;
  }
  
  .event-item:last-child {
    border-bottom: none;
  }
  
  .event-date {
    font-size: 12px;
    color: var(--gray);
    width: 60px;
  }
  
  .event-details {
    flex: 1;
  }
  
  .event-batch {
    font-size: 13px;
    font-weight: 500;
  }
  
  .event-cause {
    font-size: 12px;
    color: var(--gray);
  }
  
  .event-count {
    font-size: 13px;
    font-weight: 600;
    color: var(--danger);
  }
  
  @media (max-width: 768px) {
    .mortality-summary {
      flex-direction: column;
    }
  }
</style>
