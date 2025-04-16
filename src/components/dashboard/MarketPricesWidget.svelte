<script>
  // Market price data for South African retailers - in a real app, this would come from an API
  export let marketData = {
    currentWeek: "April 10-16, 2025",
    previousWeek: "April 3-9, 2025",
    retailers: [
      {
        name: "Shoprite",
        logo: "shoprite",
        currentPrice: 79.99,
        previousPrice: 82.99,
        change: -3.00,
        trend: "down"
      },
      {
        name: "Pick n Pay",
        logo: "pnp",
        currentPrice: 84.99,
        previousPrice: 84.99,
        change: 0,
        trend: "stable"
      },
      {
        name: "Woolworths",
        logo: "woolworths",
        currentPrice: 99.99,
        previousPrice: 94.99,
        change: 5.00,
        trend: "up"
      },
      {
        name: "SPAR",
        logo: "spar",
        currentPrice: 82.99,
        previousPrice: 79.99,
        change: 3.00,
        trend: "up"
      },
      {
        name: "Checkers",
        logo: "checkers",
        currentPrice: 77.99,
        previousPrice: 80.99,
        change: -3.00,
        trend: "down"
      }
    ],
    priceHistory: [
      { week: "Feb 12-18", avgPrice: 82.50 },
      { week: "Feb 19-25", avgPrice: 83.20 },
      { week: "Feb 26-Mar 4", avgPrice: 84.10 },
      { week: "Mar 5-11", avgPrice: 85.50 },
      { week: "Mar 12-18", avgPrice: 86.20 },
      { week: "Mar 19-25", avgPrice: 85.80 },
      { week: "Mar 26-Apr 1", avgPrice: 84.90 },
      { week: "Apr 3-9", avgPrice: 84.79 },
      { week: "Apr 10-16", avgPrice: 85.19 }
    ]
  };
  
  // Calculate average prices
  $: averageCurrentPrice = marketData.retailers.reduce((sum, retailer) => sum + retailer.currentPrice, 0) / marketData.retailers.length;
  $: averagePreviousPrice = marketData.retailers.reduce((sum, retailer) => sum + retailer.previousPrice, 0) / marketData.retailers.length;
  $: priceChange = averageCurrentPrice - averagePreviousPrice;
  $: priceTrend = priceChange > 0 ? "up" : priceChange < 0 ? "down" : "stable";
  
  // Calculate optimal selling price based on market algorithm
  // This algorithm takes the average market price and adds a small premium if our quality is higher
  // In a real app, this would be more sophisticated and consider more factors
  $: optimalPrice = averageCurrentPrice * 1.02; // 2% premium for quality
  $: potentialRevenue = optimalPrice * 5000; // Assuming 5000 birds ready for market
  
  // Calculate price volatility (standard deviation)
  $: priceVolatility = calculateVolatility(marketData.priceHistory.map(item => item.avgPrice));
  
  function calculateVolatility(prices) {
    const mean = prices.reduce((sum, price) => sum + price, 0) / prices.length;
    const squaredDiffs = prices.map(price => Math.pow(price - mean, 2));
    const variance = squaredDiffs.reduce((sum, diff) => sum + diff, 0) / prices.length;
    return Math.sqrt(variance).toFixed(2);
  }
</script>

<div class="market-prices-widget">
  <div class="price-summary">
    <div class="summary-header">
      <div class="current-week">{marketData.currentWeek}</div>
      <div class="price-trend {priceTrend}">
        <span class="material-icons">
          {#if priceTrend === 'up'}
            trending_up
          {:else if priceTrend === 'down'}
            trending_down
          {:else}
            trending_flat
          {/if}
        </span>
        <span class="trend-value">
          {#if priceChange > 0}+{/if}{priceChange.toFixed(2)} ZAR
        </span>
      </div>
    </div>
    
    <div class="average-price">
      <div class="price-value">R {averageCurrentPrice.toFixed(2)}</div>
      <div class="price-label">Average Price per kg</div>
    </div>
  </div>
  
  <div class="retailer-prices">
    {#each marketData.retailers as retailer}
      <div class="retailer-item">
        <div class="retailer-logo {retailer.logo}">
          <span class="retailer-initial">{retailer.name[0]}</span>
        </div>
        <div class="retailer-info">
          <div class="retailer-name">{retailer.name}</div>
          <div class="retailer-price">R {retailer.currentPrice.toFixed(2)}</div>
        </div>
        <div class="price-change {retailer.trend}">
          <span class="material-icons">
            {#if retailer.trend === 'up'}
              arrow_upward
            {:else if retailer.trend === 'down'}
              arrow_downward
            {:else}
              remove
            {/if}
          </span>
          <span class="change-value">
            {#if retailer.change > 0}+{/if}{retailer.change.toFixed(2)}
          </span>
        </div>
      </div>
    {/each}
  </div>
  
  <div class="price-algorithm">
    <h3 class="section-title">Market Algorithm</h3>
    <div class="algorithm-metrics">
      <div class="metric">
        <div class="metric-value">R {optimalPrice.toFixed(2)}</div>
        <div class="metric-label">Optimal Selling Price</div>
      </div>
      <div class="metric">
        <div class="metric-value">R {potentialRevenue.toLocaleString()}</div>
        <div class="metric-label">Potential Revenue</div>
      </div>
      <div class="metric">
        <div class="metric-value">{priceVolatility}</div>
        <div class="metric-label">Price Volatility</div>
      </div>
    </div>
    <div class="algorithm-note">
      Based on current market trends and competitor pricing
    </div>
  </div>
</div>

<style>
  .market-prices-widget {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  .price-summary {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .summary-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
  }
  
  .current-week {
    font-size: 14px;
    font-weight: 500;
    color: var(--gray);
  }
  
  .price-trend {
    display: flex;
    align-items: center;
    font-size: 14px;
    font-weight: 500;
  }
  
  .price-trend.up {
    color: var(--success);
  }
  
  .price-trend.down {
    color: var(--danger);
  }
  
  .price-trend.stable {
    color: var(--gray);
  }
  
  .price-trend .material-icons {
    font-size: 18px;
    margin-right: 4px;
  }
  
  .average-price {
    text-align: center;
  }
  
  .price-value {
    font-size: 28px;
    font-weight: 600;
    color: var(--dark);
    margin-bottom: 4px;
  }
  
  .price-label {
    font-size: 14px;
    color: var(--gray);
  }
  
  .retailer-prices {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .retailer-item {
    display: flex;
    align-items: center;
    padding: 8px 0;
    border-bottom: 1px solid #eee;
  }
  
  .retailer-item:last-child {
    border-bottom: none;
  }
  
  .retailer-logo {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 12px;
    color: white;
    font-weight: 600;
  }
  
  .retailer-logo.shoprite {
    background-color: #e63c2f;
  }
  
  .retailer-logo.pnp {
    background-color: #0066b2;
  }
  
  .retailer-logo.woolworths {
    background-color: #000000;
  }
  
  .retailer-logo.spar {
    background-color: #00843d;
  }
  
  .retailer-logo.checkers {
    background-color: #e63c2f;
  }
  
  .retailer-info {
    flex: 1;
  }
  
  .retailer-name {
    font-size: 14px;
    font-weight: 500;
  }
  
  .retailer-price {
    font-size: 13px;
    color: var(--gray);
  }
  
  .price-change {
    display: flex;
    align-items: center;
    font-size: 13px;
    font-weight: 500;
  }
  
  .price-change.up {
    color: var(--success);
  }
  
  .price-change.down {
    color: var(--danger);
  }
  
  .price-change.stable {
    color: var(--gray);
  }
  
  .price-change .material-icons {
    font-size: 16px;
    margin-right: 2px;
  }
  
  .price-algorithm {
    background-color: #f8f9fa;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .section-title {
    font-size: 14px;
    font-weight: 600;
    margin: 0 0 12px 0;
    color: var(--dark);
  }
  
  .algorithm-metrics {
    display: flex;
    justify-content: space-between;
    margin-bottom: 12px;
  }
  
  .metric {
    text-align: center;
    flex: 1;
  }
  
  .metric-value {
    font-size: 16px;
    font-weight: 600;
    color: var(--primary);
    margin-bottom: 4px;
  }
  
  .metric-label {
    font-size: 12px;
    color: var(--gray);
  }
  
  .algorithm-note {
    font-size: 12px;
    color: var(--gray);
    text-align: center;
    font-style: italic;
  }
  
  @media (max-width: 768px) {
    .algorithm-metrics {
      flex-direction: column;
      gap: 12px;
    }
  }
</style>
