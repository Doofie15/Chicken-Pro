<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  
  let chartCanvas;
  let chart;
  
  // Sample data - in a real app, this would come from an API
  const growthData = {
    labels: ['Day 1', 'Day 5', 'Day 10', 'Day 15', 'Day 20', 'Day 25', 'Day 30', 'Day 35', 'Day 40', 'Day 45'],
    datasets: [
      {
        label: 'Batch 45',
        data: [42, 150, 320, 580, 920, 1280, 1650, 1980, 2280, 2520],
        borderColor: '#1976d2',
        backgroundColor: 'rgba(25, 118, 210, 0.1)',
        borderWidth: 2,
        tension: 0.4,
        fill: true
      },
      {
        label: 'Batch 44',
        data: [40, 145, 310, 560, 900, 1250, 1620, 1950, 2250, 2500],
        borderColor: '#26a69a',
        backgroundColor: 'rgba(38, 166, 154, 0.1)',
        borderWidth: 2,
        tension: 0.4,
        fill: true
      },
      {
        label: 'Target',
        data: [45, 160, 340, 600, 950, 1300, 1700, 2050, 2350, 2600],
        borderColor: '#ff9800',
        backgroundColor: 'transparent',
        borderWidth: 2,
        borderDash: [5, 5],
        tension: 0.4,
        fill: false
      }
    ]
  };
  
  // @ts-ignore - Ignore Chart.js type errors
  const chartOptions = {
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
            return context.dataset.label + ': ' + context.parsed.y + 'g';
          }
        }
      }
    },
    scales: {
      x: {
        grid: {
          display: false
        },
        ticks: {
          font: {
            size: 12
          }
        }
      },
      y: {
        grid: {
          color: 'rgba(0, 0, 0, 0.05)'
        },
        ticks: {
          font: {
            size: 12
          },
          callback: function(value) {
            return value + 'g';
          }
        },
        beginAtZero: true
      }
    },
    interaction: {
      mode: 'nearest',
      axis: 'x',
      intersect: false
    },
    elements: {
      point: {
        radius: 3,
        hoverRadius: 5
      }
    }
  };
  
  onMount(() => {
    if (chartCanvas) {
      const ctx = chartCanvas.getContext('2d');
      
      chart = new Chart(ctx, {
        type: 'line',
        data: growthData,
        options: chartOptions
      });
    }
    
    return () => {
      if (chart) {
        chart.destroy();
      }
    };
  });
  
  // Toggle visibility of datasets
  function toggleDataset(index) {
    if (chart) {
      const dataset = chart.data.datasets[index];
      dataset.hidden = !dataset.hidden;
      chart.update();
    }
  }
  
  // Change chart type
  function setChartType(type) {
    if (chart) {
      chart.config.type = type;
      chart.update();
    }
  }
</script>

<div class="chart-container">
  <div class="chart-toolbar">
    <div class="chart-legend">
      {#each growthData.datasets as dataset, i}
        <button 
          class="legend-item" 
          on:click={() => toggleDataset(i)}
          style="--color: {dataset.borderColor}"
        >
          <span class="legend-marker"></span>
          <span class="legend-label">{dataset.label}</span>
        </button>
      {/each}
    </div>
    
    <div class="chart-actions">
      <button class="chart-type-btn active" on:click={() => setChartType('line')}>
        <span class="material-icons">show_chart</span>
      </button>
      <button class="chart-type-btn" on:click={() => setChartType('bar')}>
        <span class="material-icons">bar_chart</span>
      </button>
    </div>
  </div>
  
  <div class="chart-wrapper">
    <canvas bind:this={chartCanvas}></canvas>
  </div>
</div>

<style>
  .chart-container {
    width: 100%;
    height: 100%;
  }
  
  .chart-toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 16px;
  }
  
  .chart-legend {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .legend-item {
    display: flex;
    align-items: center;
    background: none;
    border: none;
    padding: 0;
    cursor: pointer;
    font-size: 12px;
    color: var(--dark);
  }
  
  .legend-marker {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background-color: var(--color);
    margin-right: 6px;
  }
  
  .chart-actions {
    display: flex;
    gap: 8px;
  }
  
  .chart-type-btn {
    width: 32px;
    height: 32px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: 1px solid var(--gray-light);
    cursor: pointer;
    color: var(--gray);
    transition: all 0.2s ease;
  }
  
  .chart-type-btn:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
  
  .chart-type-btn.active {
    background-color: var(--primary);
    border-color: var(--primary);
    color: white;
  }
  
  .chart-wrapper {
    height: 300px;
    position: relative;
  }
  
  @media (max-width: 768px) {
    .chart-toolbar {
      flex-direction: column;
      align-items: flex-start;
      gap: 12px;
    }
    
    .chart-legend {
      width: 100%;
    }
  }
</style>
