<script>
  export let orders = [];
  
  function formatCurrency(value) {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD',
      minimumFractionDigits: 0
    }).format(value);
  }
  
  function formatDate(dateString) {
    const date = new Date(dateString);
    return new Intl.DateTimeFormat('en-US', { 
      month: 'short', 
      day: 'numeric'
    }).format(date);
  }
</script>

<div class="orders-widget">
  {#each orders as order}
    <div class="order-item">
      <div class="order-info">
        <div class="order-header">
          <h3 class="order-id">{order.id}</h3>
          <span class="order-status status-{order.status}">
            {order.status}
          </span>
        </div>
        
        <p class="order-customer">{order.customer}</p>
        
        <div class="order-details">
          <div class="order-quantity">
            <span class="material-icons">inventory_2</span>
            {order.quantity.toLocaleString()} birds
          </div>
          
          <div class="order-delivery">
            <span class="material-icons">local_shipping</span>
            {formatDate(order.deliveryDate)}
          </div>
        </div>
      </div>
      
      <div class="order-value">
        <span class="value-amount">{formatCurrency(order.value)}</span>
        <button class="btn-outline-primary btn-sm">Details</button>
      </div>
    </div>
  {/each}
</div>

<style>
  .orders-widget {
    width: 100%;
  }
  
  .order-item {
    display: flex;
    justify-content: space-between;
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
    transition: background-color 0.2s ease;
  }
  
  .order-item:last-child {
    border-bottom: none;
  }
  
  .order-item:hover {
    background-color: rgba(0, 0, 0, 0.02);
  }
  
  .order-info {
    flex: 1;
  }
  
  .order-header {
    display: flex;
    align-items: center;
    margin-bottom: 4px;
  }
  
  .order-id {
    margin: 0;
    font-size: 14px;
    font-weight: 500;
    margin-right: 8px;
  }
  
  .order-status {
    font-size: 11px;
    text-transform: uppercase;
    font-weight: 500;
    padding: 2px 6px;
    border-radius: 4px;
  }
  
  .status-confirmed {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .status-pending {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .status-cancelled {
    background-color: rgba(244, 67, 54, 0.1);
    color: var(--danger);
  }
  
  .order-customer {
    margin: 0 0 8px;
    font-size: 12px;
    color: var(--gray);
  }
  
  .order-details {
    display: flex;
    gap: 16px;
  }
  
  .order-quantity,
  .order-delivery {
    display: flex;
    align-items: center;
    font-size: 12px;
    color: var(--dark);
  }
  
  .order-quantity .material-icons,
  .order-delivery .material-icons {
    font-size: 14px;
    margin-right: 4px;
    color: var(--gray);
  }
  
  .order-value {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    justify-content: space-between;
  }
  
  .value-amount {
    font-size: 16px;
    font-weight: 500;
    color: var(--dark);
    margin-bottom: 8px;
  }
  
  .btn-outline-primary {
    color: var(--primary);
    background-color: transparent;
    border: 1px solid var(--primary);
    border-radius: 4px;
    padding: 4px 12px;
    font-size: 12px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
  }
  
  .btn-outline-primary:hover {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-sm {
    padding: 4px 8px;
    font-size: 12px;
  }
</style>
