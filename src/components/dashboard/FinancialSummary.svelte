<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  
  let chartCanvas;
  let chart;
  
  // Sample data - in a real app, this would come from an API
  const financialData = {
    revenue: 45000,
    expenses: 28500,
    profit: 16500,
    profitMargin: 36.7,
    change: 8.2,
    breakdown: {
      labels: ['Feed', 'Labor', 'Utilities', 'Medicine', 'Maintenance', 'Other'],
      data: [65, 12, 8, 6, 5, 4]
    }
  };
  
  function formatCurrency(value) {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD',
      minimumFractionDigits: 0
    }).format(value);
  }
  
  onMount(() => {
    if (chartCanvas) {
      const ctx = chartCanvas.getContext('2d');
      
      chart = new Chart(ctx, {
        type: 'doughnut',
        data: {
          labels: financialData.breakdown.labels,
          datasets: [{
            data: financialData.breakdown.data,
            backgroundColor: [
              '#1976d2',
              '#26a69a',
              '#ff9800',
              '#f44336',
              '#9c27b0',
              '#607d8b'
            ],
            borderWidth: 0,
            hoverOffset: 5
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          cutout: '70%',
          plugins: {
            legend: {
              display: false
            },
            tooltip: {
              backgroundColor: 'rgba(255, 255, 255, 0.9)',
              titleColor: '#212529',
              bodyColor: '#212529',
              borderColor: '#e9ecef',
              borderWidth: 1,
              padding: 12,
              boxPadding: 6,
              usePointStyle: true,
              callbacks: {
                label: function(context) {
                  return context.label + ': ' + context.parsed + '%';
                }
              }
            }
          }
        }
      });
    }
    
    return () => {
      if (chart) {
        chart.destroy();
      }
    };
  });
</script>

<div class="financial-summary">
  <div class="financial-metrics">
    <div class="metric">
      <span class="metric-label">Revenue</span>
      <span class="metric-value">{formatCurrency(financialData.revenue)}</span>
    </div>
    
    <div class="metric">
      <span class="metric-label">Expenses</span>
      <span class="metric-value">{formatCurrency(financialData.expenses)}</span>
    </div>
    
    <div class="metric highlight">
      <span class="metric-label">Profit</span>
      <span class="metric-value">{formatCurrency(financialData.profit)}</span>
      <span class="metric-change trend-up">+{financialData.change}%</span>
    </div>
  </div>
  
  <div class="profit-margin">
    <div class="margin-info">
      <span class="margin-label">Profit Margin</span>
      <span class="margin-value">{financialData.profitMargin}%</span>
    </div>
    <div class="margin-bar">
      <div class="margin-fill" style="width: {financialData.profitMargin}%"></div>
    </div>
  </div>
  
  <div class="expense-breakdown">
    <h3 class="breakdown-title">Expense Breakdown</h3>
    
    <div class="chart-container">
      <div class="chart-wrapper">
        <canvas bind:this={chartCanvas}></canvas>
      </div>
      
      <div class="chart-legend">
        {#each financialData.breakdown.labels as label, i}
          <div class="legend-item">
            <span 
              class="legend-marker" 
              style="background-color: {['#1976d2', '#26a69a', '#ff9800', '#f44336', '#9c27b0', '#607d8b'][i]}"
            ></span>
            <span class="legend-label">{label}</span>
            <span class="legend-value">{financialData.breakdown.data[i]}%</span>
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>

<style>
  .financial-summary {
    width: 100%;
  }
  
  .financial-metrics {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    margin-bottom: 16px;
  }
  
  .metric {
    display: flex;
    flex-direction: column;
    padding: 12px;
    background-color: var(--gray-light);
    border-radius: 8px;
  }
  
  .metric.highlight {
    background-color: rgba(25, 118, 210, 0.1);
    border-left: 3px solid var(--primary);
  }
  
  .metric-label {
    font-size: 12px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .metric-value {
    font-size: 18px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .metric-change {
    font-size: 12px;
    font-weight: 500;
    margin-top: 4px;
  }
  
  .trend-up {
    color: var(--success);
  }
  
  .trend-down {
    color: var(--danger);
  }
  /* The trend-down class is used dynamically based on financial data */
  
  .profit-margin {
    margin-bottom: 24px;
  }
  
  .margin-info {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
  }
  
  .margin-label {
    font-size: 14px;
    color: var(--dark);
  }
  
  .margin-value {
    font-size: 14px;
    font-weight: 500;
    color: var(--primary);
  }
  
  .margin-bar {
    height: 8px;
    background-color: var(--gray-light);
    border-radius: 4px;
    overflow: hidden;
  }
  
  .margin-fill {
    height: 100%;
    background-color: var(--primary);
    border-radius: 4px;
  }
  
  .expense-breakdown {
    margin-top: 16px;
  }
  
  .breakdown-title {
    font-size: 14px;
    font-weight: 500;
    margin: 0 0 16px;
  }
  
  .chart-container {
    display: flex;
    align-items: center;
  }
  
  .chart-wrapper {
    width: 120px;
    height: 120px;
  }
  
  .chart-legend {
    flex: 1;
    padding-left: 16px;
  }
  
  .legend-item {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
  }
  
  .legend-marker {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    margin-right: 8px;
  }
  
  .legend-label {
    font-size: 12px;
    color: var(--dark);
    flex: 1;
  }
  
  .legend-value {
    font-size: 12px;
    font-weight: 500;
    color: var(--dark);
  }
  
  @media (max-width: 576px) {
    .financial-metrics {
      grid-template-columns: 1fr;
    }
    
    .chart-container {
      flex-direction: column;
    }
    
    .chart-wrapper {
      width: 100%;
      margin-bottom: 16px;
    }
    
    .chart-legend {
      padding-left: 0;
      width: 100%;
    }
  }
</style>
