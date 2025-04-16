<script>
  // Feed inventory data - in a real app, this would come from an API
  export let feedData = {
    totalFeedGiven: 12500,
    totalFeedCost: 8750,
    feedRemaining: {
      starter: 2800,
      grower: 4500,
      finisher: 1200
    },
    feedPrices: {
      starter: 0.75,
      grower: 0.68,
      finisher: 0.72
    },
    recentDeliveries: [
      {
        date: '2025-04-10',
        type: 'Starter',
        quantity: 2000,
        cost: 1500
      },
      {
        date: '2025-04-05',
        type: 'Grower',
        quantity: 3000,
        cost: 2040
      },
      {
        date: '2025-03-28',
        type: 'Finisher',
        quantity: 2500,
        cost: 1800
      }
    ]
  };
  
  // Calculate total feed remaining
  $: totalFeedRemaining = Object.values(feedData.feedRemaining).reduce((sum, qty) => sum + qty, 0);
  
  // Calculate average feed price
  $: avgFeedPrice = feedData.totalFeedCost / feedData.totalFeedGiven;
</script>

<div class="feed-inventory-widget">
  <div class="feed-summary">
    <div class="feed-metric">
      <div class="metric-value">{feedData.totalFeedGiven.toLocaleString()} kg</div>
      <div class="metric-label">Total Feed Given</div>
      <div class="metric-icon">
        <span class="material-icons">grass</span>
      </div>
    </div>
    
    <div class="feed-metric">
      <div class="metric-value">${feedData.totalFeedCost.toLocaleString()}</div>
      <div class="metric-label">Total Feed Cost</div>
      <div class="metric-icon">
        <span class="material-icons">attach_money</span>
      </div>
    </div>
    
    <div class="feed-metric">
      <div class="metric-value">{totalFeedRemaining.toLocaleString()} kg</div>
      <div class="metric-label">Feed Remaining</div>
      <div class="metric-icon">
        <span class="material-icons">inventory</span>
      </div>
    </div>
  </div>
  
  <div class="feed-details">
    <div class="feed-inventory">
      <h3 class="section-title">Current Inventory</h3>
      <div class="inventory-bars">
        <div class="inventory-item">
          <div class="inventory-label">
            <span>Starter</span>
            <span class="inventory-value">{feedData.feedRemaining.starter.toLocaleString()} kg</span>
          </div>
          <div class="progress-bar">
            <div class="progress-fill starter" style="width: {Math.min(100, feedData.feedRemaining.starter / 50)}%"></div>
          </div>
          <div class="inventory-price">${feedData.feedPrices.starter}/kg</div>
        </div>
        
        <div class="inventory-item">
          <div class="inventory-label">
            <span>Grower</span>
            <span class="inventory-value">{feedData.feedRemaining.grower.toLocaleString()} kg</span>
          </div>
          <div class="progress-bar">
            <div class="progress-fill grower" style="width: {Math.min(100, feedData.feedRemaining.grower / 50)}%"></div>
          </div>
          <div class="inventory-price">${feedData.feedPrices.grower}/kg</div>
        </div>
        
        <div class="inventory-item">
          <div class="inventory-label">
            <span>Finisher</span>
            <span class="inventory-value">{feedData.feedRemaining.finisher.toLocaleString()} kg</span>
          </div>
          <div class="progress-bar">
            <div class="progress-fill finisher" style="width: {Math.min(100, feedData.feedRemaining.finisher / 50)}%"></div>
          </div>
          <div class="inventory-price">${feedData.feedPrices.finisher}/kg</div>
        </div>
      </div>
    </div>
    
    <div class="feed-deliveries">
      <h3 class="section-title">Recent Deliveries</h3>
      <div class="deliveries-list">
        {#each feedData.recentDeliveries as delivery}
          <div class="delivery-item">
            <div class="delivery-icon">
              <span class="material-icons">local_shipping</span>
            </div>
            <div class="delivery-details">
              <div class="delivery-type">{delivery.type}</div>
              <div class="delivery-date">{new Date(delivery.date).toLocaleDateString()}</div>
            </div>
            <div class="delivery-quantity">
              {delivery.quantity.toLocaleString()} kg
            </div>
            <div class="delivery-cost">
              ${delivery.cost.toLocaleString()}
            </div>
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>

<style>
  .feed-inventory-widget {
    display: flex;
    flex-direction: column;
    height: 100%;
  }
  
  .feed-summary {
    display: flex;
    margin-bottom: 16px;
    gap: 12px;
  }
  
  .feed-metric {
    flex: 1;
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 16px;
    position: relative;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
    overflow: hidden;
  }
  
  .metric-value {
    font-size: 20px;
    font-weight: 600;
    margin-bottom: 4px;
    position: relative;
    z-index: 1;
  }
  
  .metric-label {
    font-size: 13px;
    color: var(--gray);
    position: relative;
    z-index: 1;
  }
  
  .metric-icon {
    position: absolute;
    top: 10px;
    right: 10px;
    opacity: 0.15;
    transform: scale(1.5);
  }
  
  .metric-icon .material-icons {
    font-size: 32px;
    color: var(--primary);
  }
  
  .feed-details {
    display: flex;
    gap: 16px;
    flex: 1;
  }
  
  .feed-inventory, .feed-deliveries {
    flex: 1;
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
  }
  
  .section-title {
    font-size: 15px;
    font-weight: 600;
    margin-top: 0;
    margin-bottom: 16px;
    color: var(--dark);
  }
  
  .inventory-bars {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }
  
  .inventory-item {
    display: flex;
    flex-direction: column;
  }
  
  .inventory-label {
    display: flex;
    justify-content: space-between;
    margin-bottom: 4px;
    font-size: 13px;
    font-weight: 500;
  }
  
  .inventory-value {
    font-weight: 600;
  }
  
  .progress-bar {
    height: 8px;
    background-color: #e9ecef;
    border-radius: 4px;
    overflow: hidden;
    margin-bottom: 4px;
  }
  
  .progress-fill {
    height: 100%;
    border-radius: 4px;
  }
  
  .progress-fill.starter {
    background: linear-gradient(90deg, #4caf50, #8bc34a);
  }
  
  .progress-fill.grower {
    background: linear-gradient(90deg, #2196f3, #03a9f4);
  }
  
  .progress-fill.finisher {
    background: linear-gradient(90deg, #ff9800, #ffb74d);
  }
  
  .inventory-price {
    text-align: right;
    font-size: 12px;
    color: var(--gray);
  }
  
  .deliveries-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
  
  .delivery-item {
    display: flex;
    align-items: center;
    padding: 8px;
    background-color: white;
    border-radius: 6px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
  }
  
  .delivery-icon {
    margin-right: 12px;
    color: var(--primary);
  }
  
  .delivery-details {
    flex: 1;
  }
  
  .delivery-type {
    font-weight: 500;
    font-size: 13px;
  }
  
  .delivery-date {
    font-size: 12px;
    color: var(--gray);
  }
  
  .delivery-quantity {
    font-size: 13px;
    margin-right: 12px;
    font-weight: 500;
  }
  
  .delivery-cost {
    font-size: 13px;
    font-weight: 600;
    color: var(--success);
    min-width: 70px;
    text-align: right;
  }
  
  @media (max-width: 768px) {
    .feed-summary {
      flex-direction: column;
    }
    
    .feed-details {
      flex-direction: column;
    }
  }
</style>
