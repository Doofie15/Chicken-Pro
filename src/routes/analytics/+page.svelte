<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  
  let performanceChartCanvas;
  let performanceChart;
  let mortalityChartCanvas;
  let mortalityChart;
  let feedConversionChartCanvas;
  let feedConversionChart;
  let weightDistributionChartCanvas;
  let weightDistributionChart;
  
  // Sample data - in a real app, this would come from an API
  const analyticsData = {
    performanceComparison: {
      labels: ['Week 1', 'Week 2', 'Week 3', 'Week 4', 'Week 5', 'Week 6', 'Week 7'],
      datasets: [
        {
          label: 'Current Batch',
          data: [150, 380, 720, 1150, 1580, 2050, 2450],
          borderColor: 'rgba(25, 118, 210, 1)',
          backgroundColor: 'rgba(25, 118, 210, 0.1)',
          fill: true,
          tension: 0.4
        },
        {
          label: 'Previous Batch',
          data: [145, 370, 700, 1120, 1540, 2000, 2400],
          borderColor: 'rgba(38, 166, 154, 1)',
          backgroundColor: 'rgba(38, 166, 154, 0.1)',
          fill: true,
          tension: 0.4
        },
        {
          label: 'Industry Average',
          data: [140, 360, 680, 1100, 1520, 1980, 2350],
          borderColor: 'rgba(255, 152, 0, 1)',
          borderDash: [5, 5],
          backgroundColor: 'transparent',
          fill: false,
          tension: 0.4
        }
      ]
    },
    mortalityTrends: {
      labels: ['Batch 40', 'Batch 41', 'Batch 42', 'Batch 43', 'Batch 44', 'Batch 45'],
      datasets: [
        {
          label: 'Daily Mortality Rate (%)',
          data: [0.18, 0.15, 0.12, 0.14, 0.16, 0.13],
          borderColor: 'rgba(244, 67, 54, 1)',
          backgroundColor: 'rgba(244, 67, 54, 0.6)',
          type: 'bar'
        },
        {
          label: 'Cumulative Mortality (%)',
          data: [2.8, 2.5, 2.2, 2.4, 2.6, 2.3],
          borderColor: 'rgba(244, 67, 54, 1)',
          backgroundColor: 'transparent',
          type: 'line',
          yAxisID: 'y1'
        }
      ]
    },
    feedConversionRatio: {
      labels: ['Batch 40', 'Batch 41', 'Batch 42', 'Batch 43', 'Batch 44', 'Batch 45'],
      datasets: [
        {
          label: 'FCR',
          data: [1.72, 1.68, 1.71, 1.69, 1.72, 1.65],
          borderColor: 'rgba(25, 118, 210, 1)',
          backgroundColor: 'rgba(25, 118, 210, 0.6)',
          type: 'bar'
        },
        {
          label: 'Target FCR',
          data: [1.7, 1.7, 1.7, 1.7, 1.7, 1.7],
          borderColor: 'rgba(255, 152, 0, 1)',
          backgroundColor: 'transparent',
          borderDash: [5, 5],
          type: 'line'
        }
      ]
    },
    weightDistribution: {
      labels: ['1800-2000g', '2000-2200g', '2200-2400g', '2400-2600g', '2600-2800g', '2800-3000g'],
      datasets: [
        {
          label: 'Batch 45',
          data: [5, 15, 35, 30, 12, 3],
          backgroundColor: 'rgba(25, 118, 210, 0.6)',
          borderColor: 'rgba(25, 118, 210, 1)',
          borderWidth: 1
        }
      ]
    },
    keyMetrics: [
      {
        title: 'Growth Rate',
        value: '62.5 g/day',
        change: '+2.3%',
        trend: 'up',
        description: 'Average daily weight gain'
      },
      {
        title: 'Feed Efficiency',
        value: '1.65 FCR',
        change: '-4.1%',
        trend: 'down',
        description: 'Feed conversion ratio'
      },
      {
        title: 'Mortality Rate',
        value: '2.3%',
        change: '-0.3%',
        trend: 'down',
        description: 'Cumulative mortality'
      },
      {
        title: 'Production Cost',
        value: '$0.92/kg',
        change: '-3.2%',
        trend: 'down',
        description: 'Cost per kg of live weight'
      }
    ],
    predictions: [
      {
        title: 'Optimal Harvest Date',
        value: 'Nov 25, 2023',
        confidence: 'High',
        description: 'Based on growth rate and market prices'
      },
      {
        title: 'Projected Final Weight',
        value: '2.65 kg',
        confidence: 'Medium',
        description: 'Average weight at 45 days'
      },
      {
        title: 'Estimated FCR',
        value: '1.67',
        confidence: 'High',
        description: 'Final feed conversion ratio'
      },
      {
        title: 'Projected Profit',
        value: '$1.28/bird',
        confidence: 'Medium',
        description: 'Based on current market prices'
      }
    ]
  };
  
  onMount(() => {
    if (performanceChartCanvas) {
      const ctx = performanceChartCanvas.getContext('2d');
      
      performanceChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: analyticsData.performanceComparison.labels,
          // @ts-ignore - Ignore Chart.js type errors
          datasets: analyticsData.performanceComparison.datasets
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              position: 'top',
              labels: {
                usePointStyle: true,
                boxWidth: 6,
                font: {
                  size: 12
                }
              }
            },
            tooltip: {
              mode: 'index',
              intersect: false,
              callbacks: {
                label: function(context) {
                  return context.dataset.label + ': ' + context.parsed.y + 'g';
                }
              }
            }
          },
          scales: {
            x: {
              grid: {
                display: false
              }
            },
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: 'Weight (g)'
              }
            }
          }
        }
      });
    }
    
    if (mortalityChartCanvas) {
      const ctx = mortalityChartCanvas.getContext('2d');
      
      mortalityChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: analyticsData.mortalityTrends.labels,
          // @ts-ignore - Ignore Chart.js type errors
          datasets: analyticsData.mortalityTrends.datasets
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              position: 'top',
              labels: {
                usePointStyle: true,
                boxWidth: 6,
                font: {
                  size: 12
                }
              }
            }
          },
          scales: {
            x: {
              grid: {
                display: false
              }
            },
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: 'Daily Mortality (%)'
              },
              max: 0.3
            },
            y1: {
              position: 'right',
              beginAtZero: true,
              title: {
                display: true,
                text: 'Cumulative Mortality (%)'
              },
              grid: {
                display: false
              },
              max: 5
            }
          }
        }
      });
    }
    
    if (feedConversionChartCanvas) {
      const ctx = feedConversionChartCanvas.getContext('2d');
      
      feedConversionChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: analyticsData.feedConversionRatio.labels,
          // @ts-ignore - Ignore Chart.js type errors
          datasets: analyticsData.feedConversionRatio.datasets
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              position: 'top',
              labels: {
                usePointStyle: true,
                boxWidth: 6,
                font: {
                  size: 12
                }
              }
            }
          },
          scales: {
            x: {
              grid: {
                display: false
              }
            },
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: 'Feed Conversion Ratio'
              },
              min: 1.5,
              max: 2.0
            }
          }
        }
      });
    }
    
    if (weightDistributionChartCanvas) {
      const ctx = weightDistributionChartCanvas.getContext('2d');
      
      weightDistributionChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: analyticsData.weightDistribution.labels,
          // @ts-ignore - Ignore Chart.js type errors
          datasets: analyticsData.weightDistribution.datasets
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: false
            },
            tooltip: {
              callbacks: {
                label: function(context) {
                  return context.parsed.y + '% of birds';
                }
              }
            }
          },
          scales: {
            x: {
              title: {
                display: true,
                text: 'Weight Range'
              }
            },
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: 'Percentage of Birds (%)'
              },
              max: 40
            }
          }
        }
      });
    }
    
    return () => {
      if (performanceChart) performanceChart.destroy();
      if (mortalityChart) mortalityChart.destroy();
      if (feedConversionChart) feedConversionChart.destroy();
      if (weightDistributionChart) weightDistributionChart.destroy();
    };
  });
</script>

<svelte:head>
  <title>Analytics | BroilerPro</title>
</svelte:head>

<div class="analytics-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Analytics</h1>
      <p class="page-subtitle">Advanced insights and performance metrics</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-primary">
        <span class="material-icons">add_chart</span>
        Custom Report
      </button>
      <button class="btn btn-outline-secondary ms-2">
        <span class="material-icons">file_download</span>
        Export
      </button>
    </div>
  </div>
  
  <div class="metrics-grid">
    {#each analyticsData.keyMetrics as metric}
      <div class="metric-card">
        <div class="metric-header">
          <h3 class="metric-title">{metric.title}</h3>
          <span class="metric-change trend-{metric.trend}">
            {metric.change}
            <span class="material-icons trend-icon">
              {metric.trend === 'up' ? 'arrow_upward' : 'arrow_downward'}
            </span>
          </span>
        </div>
        <div class="metric-value">{metric.value}</div>
        <div class="metric-description">{metric.description}</div>
      </div>
    {/each}
  </div>
  
  <div class="dashboard-grid">
    <div class="grid-col-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Growth Performance Comparison</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>Batch 45</option>
              <option>Batch 44</option>
              <option>Batch 43</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={performanceChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Mortality Trends</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>Last 6 Batches</option>
              <option>Last 12 Batches</option>
              <option>Year to Date</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={mortalityChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Feed Conversion Ratio</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>Last 6 Batches</option>
              <option>Last 12 Batches</option>
              <option>Year to Date</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={feedConversionChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Weight Distribution</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>Batch 45</option>
              <option>Batch 44</option>
              <option>Batch 43</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={weightDistributionChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">AI Predictions</h2>
          <div class="card-actions">
            <button class="btn-icon" title="Refresh Predictions">
              <span class="material-icons">refresh</span>
            </button>
          </div>
        </div>
        <div class="card-body p-0">
          <div class="predictions-list">
            {#each analyticsData.predictions as prediction}
              <div class="prediction-item">
                <div class="prediction-content">
                  <h3 class="prediction-title">{prediction.title}</h3>
                  <p class="prediction-description">{prediction.description}</p>
                </div>
                <div class="prediction-value">
                  <div class="value">{prediction.value}</div>
                  <div class="confidence confidence-{prediction.confidence.toLowerCase()}">
                    {prediction.confidence} confidence
                  </div>
                </div>
              </div>
            {/each}
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .analytics-page {
    padding: 16px;
  }
  
  .page-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .page-title {
    font-size: 24px;
    font-weight: 500;
    margin: 0;
    color: var(--dark);
  }
  
  .page-subtitle {
    font-size: 14px;
    color: var(--gray);
    margin: 4px 0 0;
  }
  
  .page-actions {
    display: flex;
    gap: 8px;
  }
  
  .metrics-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
  }
  
  .metric-card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .metric-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .metric-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
  }
  
  .metric-title {
    font-size: 14px;
    font-weight: 500;
    margin: 0;
    color: var(--gray);
  }
  
  .metric-change {
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
  
  .metric-value {
    font-size: 24px;
    font-weight: 500;
    margin-bottom: 8px;
    color: var(--dark);
  }
  
  .metric-description {
    font-size: 12px;
    color: var(--gray);
  }
  
  .dashboard-grid {
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    gap: 16px;
  }
  
  .grid-col-12 {
    grid-column: span 12;
  }
  
  .grid-col-6 {
    grid-column: span 6;
  }
  
  .card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    height: 100%;
    transition: box-shadow 0.3s ease, transform 0.3s ease;
    margin-bottom: 16px;
  }
  
  .card:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    transform: translateY(-2px);
  }
  
  .card-header {
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .card-title {
    font-size: 16px;
    font-weight: 500;
    margin: 0;
  }
  
  .card-actions {
    display: flex;
    align-items: center;
  }
  
  .card-body {
    padding: 16px;
  }
  
  .p-0 {
    padding: 0 !important;
  }
  
  .chart-container {
    height: 300px;
    position: relative;
  }
  
  .form-select-sm {
    font-size: 14px;
    padding: 4px 24px 4px 8px;
    height: 32px;
  }
  
  .btn-icon {
    width: 32px;
    height: 32px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--gray);
    transition: all 0.2s ease;
  }
  
  .btn-icon:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--primary);
  }
  
  .predictions-list {
    display: flex;
    flex-direction: column;
  }
  
  .prediction-item {
    display: flex;
    justify-content: space-between;
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .prediction-item:last-child {
    border-bottom: none;
  }
  
  .prediction-content {
    flex: 1;
  }
  
  .prediction-title {
    font-size: 14px;
    font-weight: 500;
    margin: 0 0 4px;
    color: var(--dark);
  }
  
  .prediction-description {
    font-size: 12px;
    color: var(--gray);
    margin: 0;
  }
  
  .prediction-value {
    text-align: right;
    margin-left: 16px;
  }
  
  .prediction-value .value {
    font-size: 16px;
    font-weight: 500;
    color: var(--primary);
    margin-bottom: 4px;
  }
  
  .confidence {
    font-size: 11px;
    padding: 2px 6px;
    border-radius: 4px;
    text-transform: lowercase;
  }
  
  .confidence-high {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .confidence-medium {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .confidence-low {
    background-color: rgba(244, 67, 54, 0.1);
    color: var(--danger);
  }
  
  @media (max-width: 992px) {
    .metrics-grid {
      grid-template-columns: repeat(2, 1fr);
    }
    
    .grid-col-md-12 {
      grid-column: span 12;
    }
  }
  
  @media (max-width: 768px) {
    .page-header {
      flex-direction: column;
      align-items: flex-start;
    }
  }
  
  @media (max-width: 576px) {
    .metrics-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
