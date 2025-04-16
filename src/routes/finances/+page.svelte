<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  
  let revenueChartCanvas;
  let revenueChart;
  let expensesChartCanvas;
  let expensesChart;
  
  // Sample data - in a real app, this would come from an API
  const financialData = {
    summary: {
      revenue: 145000,
      expenses: 92000,
      profit: 53000,
      profitMargin: 36.6,
      pendingInvoices: 28000,
      overdueInvoices: 12000
    },
    revenueByMonth: {
      labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
      data: [32000, 28000, 35000, 30000, 38000, 42000, 40000, 45000, 48000, 50000, 52000, 55000]
    },
    expensesByCategory: {
      labels: ['Feed', 'Labor', 'Utilities', 'Medicine', 'Maintenance', 'Other'],
      data: [45000, 20000, 8000, 6000, 5000, 8000]
    },
    recentTransactions: [
      {
        id: 'TRX-2023-112',
        date: '2023-11-10',
        description: 'Payment from Metro Supermarket',
        amount: 12500,
        type: 'income'
      },
      {
        id: 'TRX-2023-111',
        date: '2023-11-08',
        description: 'Feed purchase - Premium Poultry Feed',
        amount: 8500,
        type: 'expense'
      },
      {
        id: 'TRX-2023-110',
        date: '2023-11-05',
        description: 'Payment from Green Valley Foods',
        amount: 15000,
        type: 'income'
      },
      {
        id: 'TRX-2023-109',
        date: '2023-11-03',
        description: 'Utility bills - Electricity',
        amount: 1200,
        type: 'expense'
      },
      {
        id: 'TRX-2023-108',
        date: '2023-11-01',
        description: 'Staff salaries',
        amount: 5800,
        type: 'expense'
      }
    ],
    invoices: [
      {
        id: 'INV-2023-056',
        customer: 'Metro Supermarket',
        issueDate: '2023-11-02',
        dueDate: '2023-11-16',
        amount: 12500,
        status: 'paid'
      },
      {
        id: 'INV-2023-055',
        customer: 'Family Poultry',
        issueDate: '2023-10-28',
        dueDate: '2023-11-11',
        amount: 9000,
        status: 'pending'
      },
      {
        id: 'INV-2023-054',
        customer: 'Green Valley Foods',
        issueDate: '2023-10-25',
        dueDate: '2023-11-08',
        amount: 15000,
        status: 'paid'
      },
      {
        id: 'INV-2023-053',
        customer: 'Sunshine Markets',
        issueDate: '2023-10-15',
        dueDate: '2023-10-29',
        amount: 8500,
        status: 'overdue'
      },
      {
        id: 'INV-2023-052',
        customer: 'Fresh Farms Co-op',
        issueDate: '2023-10-10',
        dueDate: '2023-10-24',
        amount: 12000,
        status: 'overdue'
      }
    ]
  };
  
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
      day: 'numeric',
      year: 'numeric'
    }).format(date);
  }
  
  onMount(() => {
    if (revenueChartCanvas) {
      const ctx = revenueChartCanvas.getContext('2d');
      
      revenueChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: financialData.revenueByMonth.labels,
          datasets: [{
            label: 'Revenue',
            data: financialData.revenueByMonth.data,
            backgroundColor: 'rgba(25, 118, 210, 0.6)',
            borderColor: 'rgba(25, 118, 210, 1)',
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: false
            },
            tooltip: {
              mode: 'index',
              intersect: false,
              callbacks: {
                label: function(context) {
                  return formatCurrency(context.parsed.y);
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
              ticks: {
                callback: function(value) {
                  return '$' + value / 1000 + 'k';
                }
              }
            }
          }
        }
      });
    }
    
    if (expensesChartCanvas) {
      const ctx = expensesChartCanvas.getContext('2d');
      
      expensesChart = new Chart(ctx, {
        type: 'doughnut',
        data: {
          labels: financialData.expensesByCategory.labels,
          datasets: [{
            data: financialData.expensesByCategory.data,
            backgroundColor: [
              'rgba(25, 118, 210, 0.8)',
              'rgba(38, 166, 154, 0.8)',
              'rgba(255, 152, 0, 0.8)',
              'rgba(244, 67, 54, 0.8)',
              'rgba(156, 39, 176, 0.8)',
              'rgba(96, 125, 139, 0.8)'
            ],
            borderWidth: 0
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              position: 'right',
              labels: {
                boxWidth: 12,
                padding: 20,
                font: {
                  size: 12
                }
              }
            },
            tooltip: {
              callbacks: {
                label: function(context) {
                  const value = context.parsed;
                  const total = context.dataset.data.reduce((a, b) => a + b, 0);
                  const percentage = Math.round((value / total) * 100);
                  return `${context.label}: ${formatCurrency(value)} (${percentage}%)`;
                }
              }
            }
          },
          cutout: '70%'
        }
      });
    }
    
    return () => {
      if (revenueChart) {
        revenueChart.destroy();
      }
      if (expensesChart) {
        expensesChart.destroy();
      }
    };
  });
</script>

<svelte:head>
  <title>Finances | BroilerPro</title>
</svelte:head>

<div class="finances-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Finances</h1>
      <p class="page-subtitle">Track your farm's financial performance</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-primary">
        <span class="material-icons">add</span>
        New Transaction
      </button>
      <button class="btn btn-outline-secondary ms-2">
        <span class="material-icons">file_download</span>
        Export
      </button>
    </div>
  </div>
  
  <div class="financial-summary">
    <div class="summary-card revenue">
      <div class="summary-icon">
        <span class="material-icons">payments</span>
      </div>
      <div class="summary-content">
        <h3 class="summary-value">{formatCurrency(financialData.summary.revenue)}</h3>
        <p class="summary-label">Total Revenue</p>
      </div>
    </div>
    
    <div class="summary-card expenses">
      <div class="summary-icon">
        <span class="material-icons">shopping_cart</span>
      </div>
      <div class="summary-content">
        <h3 class="summary-value">{formatCurrency(financialData.summary.expenses)}</h3>
        <p class="summary-label">Total Expenses</p>
      </div>
    </div>
    
    <div class="summary-card profit">
      <div class="summary-icon">
        <span class="material-icons">trending_up</span>
      </div>
      <div class="summary-content">
        <h3 class="summary-value">{formatCurrency(financialData.summary.profit)}</h3>
        <p class="summary-label">Net Profit</p>
        <span class="profit-margin">Margin: {financialData.summary.profitMargin}%</span>
      </div>
    </div>
    
    <div class="summary-card invoices">
      <div class="summary-icon">
        <span class="material-icons">receipt</span>
      </div>
      <div class="summary-content">
        <h3 class="summary-value">{formatCurrency(financialData.summary.pendingInvoices)}</h3>
        <p class="summary-label">Pending Invoices</p>
        <span class="overdue-amount">Overdue: {formatCurrency(financialData.summary.overdueInvoices)}</span>
      </div>
    </div>
  </div>
  
  <div class="dashboard-grid">
    <div class="grid-col-8 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Revenue by Month</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>This Year</option>
              <option>Last Year</option>
              <option>Last 12 Months</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={revenueChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Expenses by Category</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>This Year</option>
              <option>Last Year</option>
              <option>Last 12 Months</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <div class="chart-container">
            <canvas bind:this={expensesChartCanvas}></canvas>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Recent Transactions</h2>
          <div class="card-actions">
            <a href="/finances/transactions" class="btn-link">View All</a>
          </div>
        </div>
        <div class="card-body p-0">
          <div class="table-responsive">
            <table class="table">
              <thead>
                <tr>
                  <th>Date</th>
                  <th>Description</th>
                  <th>Amount</th>
                </tr>
              </thead>
              <tbody>
                {#each financialData.recentTransactions as transaction}
                  <tr>
                    <td>{formatDate(transaction.date)}</td>
                    <td>
                      <div class="transaction-info">
                        <span class="transaction-id">{transaction.id}</span>
                        <span class="transaction-desc">{transaction.description}</span>
                      </div>
                    </td>
                    <td>
                      <span class="transaction-amount {transaction.type}">
                        {transaction.type === 'income' ? '+' : '-'} {formatCurrency(transaction.amount)}
                      </span>
                    </td>
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    
    <div class="grid-col-6 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Recent Invoices</h2>
          <div class="card-actions">
            <a href="/finances/invoices" class="btn-link">View All</a>
          </div>
        </div>
        <div class="card-body p-0">
          <div class="table-responsive">
            <table class="table">
              <thead>
                <tr>
                  <th>Invoice</th>
                  <th>Customer</th>
                  <th>Due Date</th>
                  <th>Amount</th>
                  <th>Status</th>
                </tr>
              </thead>
              <tbody>
                {#each financialData.invoices as invoice}
                  <tr>
                    <td>{invoice.id}</td>
                    <td>{invoice.customer}</td>
                    <td>{formatDate(invoice.dueDate)}</td>
                    <td>{formatCurrency(invoice.amount)}</td>
                    <td>
                      <span class="status-badge status-{invoice.status}">
                        {invoice.status}
                      </span>
                    </td>
                  </tr>
                {/each}
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .finances-page {
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
  
  .financial-summary {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
  }
  
  .summary-card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    display: flex;
    align-items: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    position: relative;
    overflow: hidden;
  }
  
  .summary-card::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 4px;
    height: 100%;
  }
  
  .summary-card.revenue::before {
    background-color: var(--primary);
  }
  
  .summary-card.expenses::before {
    background-color: var(--warning);
  }
  
  .summary-card.profit::before {
    background-color: var(--success);
  }
  
  .summary-card.invoices::before {
    background-color: var(--info);
  }
  
  .summary-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .summary-icon {
    width: 48px;
    height: 48px;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .summary-card.revenue .summary-icon {
    background-color: rgba(25, 118, 210, 0.1);
    color: var(--primary);
  }
  
  .summary-card.expenses .summary-icon {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .summary-card.profit .summary-icon {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .summary-card.invoices .summary-icon {
    background-color: rgba(33, 150, 243, 0.1);
    color: var(--info);
  }
  
  .summary-icon .material-icons {
    font-size: 24px;
  }
  
  .summary-content {
    flex: 1;
  }
  
  .summary-value {
    font-size: 20px;
    font-weight: 500;
    margin: 0 0 4px;
    color: var(--dark);
  }
  
  .summary-label {
    font-size: 12px;
    color: var(--gray);
    margin: 0 0 4px;
  }
  
  .profit-margin {
    font-size: 12px;
    color: var(--success);
    font-weight: 500;
  }
  
  .overdue-amount {
    font-size: 12px;
    color: var(--danger);
    font-weight: 500;
  }
  
  .dashboard-grid {
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    gap: 16px;
  }
  
  .grid-col-8 {
    grid-column: span 8;
  }
  
  .grid-col-6 {
    grid-column: span 6;
  }
  
  .grid-col-4 {
    grid-column: span 4;
  }
  
  .card {
    background: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    height: 100%;
    transition: box-shadow 0.3s ease, transform 0.3s ease;
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
  
  .btn-link {
    color: var(--primary);
    text-decoration: none;
    font-size: 14px;
    font-weight: 500;
  }
  
  .form-select-sm {
    font-size: 14px;
    padding: 4px 24px 4px 8px;
    height: 32px;
  }
  
  .table {
    margin-bottom: 0;
  }
  
  .table th {
    font-weight: 500;
    color: var(--gray);
    border-top: none;
    padding: 12px 16px;
  }
  
  .table td {
    vertical-align: middle;
    padding: 12px 16px;
  }
  
  .transaction-info {
    display: flex;
    flex-direction: column;
  }
  
  .transaction-id {
    font-size: 12px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .transaction-desc {
    font-size: 14px;
    color: var(--dark);
  }
  
  .transaction-amount {
    font-weight: 500;
  }
  
  .transaction-amount.income {
    color: var(--success);
  }
  
  .transaction-amount.expense {
    color: var(--danger);
  }
  
  .status-badge {
    display: inline-block;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
    text-transform: capitalize;
  }
  
  .status-paid {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .status-pending {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .status-overdue {
    background-color: rgba(244, 67, 54, 0.1);
    color: var(--danger);
  }
  
  @media (max-width: 992px) {
    .financial-summary {
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
    .financial-summary {
      grid-template-columns: 1fr;
    }
  }
</style>
