<script>
  import { onMount } from 'svelte';
  import DashboardStats from '../components/dashboard/DashboardStats.svelte';
  import ActiveFlockCard from '../components/flock/ActiveFlockCard.svelte';
  import PerformanceChart from '../components/dashboard/PerformanceChart.svelte';
  import ActivityWidget from '../components/dashboard/ActivityWidget.svelte';
  import CustomerOrdersWidget from '../components/dashboard/CustomerOrdersWidget.svelte';
  import FinancialSummary from '../components/dashboard/FinancialSummary.svelte';
  import WeatherWidget from '../components/dashboard/WeatherWidget.svelte';
  import AlertsWidget from '../components/dashboard/AlertsWidget.svelte';
  import FeedInventoryWidget from '../components/dashboard/FeedInventoryWidget.svelte';
  import MortalityWidget from '../components/dashboard/MortalityWidget.svelte';
  import MarketPricesWidget from '../components/dashboard/MarketPricesWidget.svelte';
  
  // Modal states for feed and mortality forms
  let showFeedModal = false;
  let showMortalityModal = false;
  
  // Handle batch form submission
  function handleBatchSubmit(event) {
    const batchData = event.detail;
    console.log('New batch created:', batchData);
    // In a real app, this would send the data to an API
    // and update the active flocks list
    
    // For demo purposes, we'll add it to the active flocks
    // Note: In a real app, this would be handled by a store or API call
    // We're just simulating the behavior here
    
    // Close the modal
    // showBatchModal = false;
    
    // Show success message
    alert(`Batch ${batchData.name} created successfully!`);
  }
  
  // Handle batch form cancellation
  function handleBatchCancel() {
    // showBatchModal = false;
  }
  
  // Sample data - in a real app, this would come from an API
  const stats = [
    { 
      title: 'Active Flocks', 
      value: '4', 
      change: '+1', 
      trend: 'up', 
      icon: 'pets',
      color: 'primary'
    },
    { 
      title: 'Total Birds', 
      value: '24,500', 
      change: '+2,500', 
      trend: 'up', 
      icon: 'egg',
      color: 'info'
    },
    { 
      title: 'Avg. Mortality', 
      value: '2.3%', 
      change: '-0.5%', 
      trend: 'down', 
      icon: 'trending_down',
      color: 'success'
    },
    { 
      title: 'Feed Conversion', 
      value: '1.68', 
      change: '-0.02', 
      trend: 'down', 
      icon: 'restaurant',
      color: 'success'
    }
  ];
  
  let activeFlocks = [
    {
      id: 'B-2023-45',
      name: 'Batch 45',
      startDate: '2023-10-15',
      age: 28,
      initialCount: 6500,
      currentCount: 6370,
      mortality: 2.0,
      avgWeight: 1450,
      targetWeight: 2500,
      progress: 58,
      feedConversion: 1.65,
      status: 'healthy',
      image: 'https://images.unsplash.com/photo-1569913486515-b74bf7751574?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=689&q=80'
    },
    {
      id: 'B-2023-44',
      name: 'Batch 44',
      startDate: '2023-09-28',
      age: 45,
      initialCount: 6000,
      currentCount: 5850,
      mortality: 2.5,
      avgWeight: 2250,
      targetWeight: 2500,
      progress: 90,
      feedConversion: 1.72,
      status: 'warning',
      image: 'https://images.unsplash.com/photo-1548550023-2bdb3c5beed7?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=687&q=80'
    }
  ];
  
  const activities = [
    {
      user: 'Dr. Sarah Johnson',
      action: 'completed vaccination',
      description: 'Completed vaccination for Batch 46 - all birds healthy',
      time: '15m ago',
      icon: 'medical_services',
      iconColor: '#1976d2'
    },
    {
      user: 'John Martinez',
      action: 'updated feed inventory',
      description: 'Added 2.5 tons of starter feed to inventory',
      time: '42m ago',
      icon: 'inventory_2',
      iconColor: '#0288d1'
    },
    {
      user: 'Michael Chen',
      action: 'processed new order',
      description: 'New order from Metro Supermarket for 3,000 birds',
      time: '1h ago',
      icon: 'shopping_cart',
      iconColor: '#7b1fa2'
    },
    {
      user: 'Farm Manager',
      action: 'approved expense report',
      description: 'Monthly utility expenses approved for payment',
      time: '3h ago',
      icon: 'payments',
      iconColor: '#0097a7'
    },
    {
      user: 'Robert Wilson',
      action: 'recorded new flock data',
      description: 'Batch 45 weight measurement: avg 1,520g, mortality 1.8%',
      time: '4h ago',
      icon: 'pets',
      iconColor: '#388e3c'
    }
  ];
  
  const orders = [
    {
      id: 'ORD-2023-089',
      customer: 'Metro Supermarket',
      quantity: 2500,
      status: 'confirmed',
      deliveryDate: '2023-11-15',
      value: 12500
    },
    {
      id: 'ORD-2023-088',
      customer: 'Family Poultry',
      quantity: 1800,
      status: 'pending',
      deliveryDate: '2023-11-18',
      value: 9000
    },
    {
      id: 'ORD-2023-087',
      customer: 'Green Valley Foods',
      quantity: 3000,
      status: 'confirmed',
      deliveryDate: '2023-11-20',
      value: 15000
    }
  ];
  
  const alerts = [
    {
      id: 1,
      type: 'warning',
      message: 'Batch 44 feed consumption below target',
      time: '35 minutes ago'
    },
    {
      id: 2,
      type: 'danger',
      message: 'Temperature in House 2 above threshold',
      time: '1 hour ago'
    },
    {
      id: 3,
      type: 'info',
      message: 'Batch 46 scheduled for vaccination tomorrow',
      time: '3 hours ago'
    }
  ];
  
  let greeting = '';
  let currentTime = new Date();
  
  onMount(() => {
    const hour = currentTime.getHours();
    
    if (hour < 12) {
      greeting = 'Good Morning';
    } else if (hour < 18) {
      greeting = 'Good Afternoon';
    } else {
      greeting = 'Good Evening';
    }
    
    // In a real app, this is where you'd fetch data from APIs
  });
</script>

<svelte:head>
  <title>Dashboard | BroilerPro</title>
</svelte:head>

<div class="dashboard">
  <div class="dashboard-header">
    <div>
      <h1 class="dashboard-title">{greeting}, Farm Manager</h1>
      <p class="dashboard-subtitle">Here's what's happening with your farm today</p>
    </div>
    <div class="dashboard-actions">
      <a href="/flock/add" class="btn btn-primary">
        <span class="material-icons">add</span>
        Add New Batch
      </a>
      <button class="btn btn-success ms-2" on:click={() => showFeedModal = true}>
        <span class="material-icons">grass</span>
        Add Feed
      </button>
      <button class="btn btn-danger ms-2" on:click={() => showMortalityModal = true}>
        <span class="material-icons">monitor_heart</span>
        Log Mortality
      </button>
      <button class="btn btn-outline-secondary ms-2">
        <span class="material-icons">file_download</span>
        Export
      </button>
    </div>
  </div>
  
  <DashboardStats {stats} />
  
  <div class="dashboard-grid">
    <div class="grid-col-8 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Growth Performance</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>Last 30 Days</option>
              <option>Last 60 Days</option>
              <option>Last 90 Days</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <PerformanceChart />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Weather Conditions</h2>
          <div class="card-actions">
            <button class="btn-icon">
              <span class="material-icons">refresh</span>
            </button>
          </div>
        </div>
        <div class="card-body">
          <WeatherWidget />
        </div>
      </div>
    </div>
    
    <div class="grid-col-8 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Feed Inventory</h2>
          <div class="card-actions">
            <button class="btn-icon">
              <span class="material-icons">refresh</span>
            </button>
            <a href="/inventory" class="btn-link ms-2">View Details</a>
          </div>
        </div>
        <div class="card-body">
          <FeedInventoryWidget />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-6 grid-col-sm-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Mortality Tracking</h2>
          <div class="card-actions">
            <button class="btn-icon">
              <span class="material-icons">trending_down</span>
            </button>
            <a href="/mortality" class="btn-link ms-2">Details</a>
          </div>
        </div>
        <div class="card-body">
          <MortalityWidget />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Active Flocks</h2>
          <div class="card-actions">
            <a href="/flock/active" class="btn-link">View All</a>
          </div>
        </div>
        <div class="card-body p-0">
          {#each activeFlocks as flock}
            <ActiveFlockCard {flock} />
          {/each}
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Recent Activity</h2>
          <div class="card-actions">
            <a href="/activity" class="btn-link">View All</a>
          </div>
        </div>
        <div class="card-body p-0">
          <ActivityWidget activities={activities} />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Market Prices</h2>
          <div class="card-actions">
            <button class="btn-icon">
              <span class="material-icons">refresh</span>
            </button>
            <a href="/market" class="btn-link ms-2">Analysis</a>
          </div>
        </div>
        <div class="card-body">
          <MarketPricesWidget />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-6 grid-col-sm-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Financial Summary</h2>
          <div class="card-actions">
            <select class="form-select form-select-sm">
              <option>This Month</option>
              <option>Last Month</option>
              <option>This Quarter</option>
            </select>
          </div>
        </div>
        <div class="card-body">
          <FinancialSummary />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-6 grid-col-sm-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Recent Orders</h2>
          <div class="card-actions">
            <a href="/customers/orders" class="btn-link">View All</a>
          </div>
        </div>
        <div class="card-body p-0">
          <CustomerOrdersWidget {orders} />
        </div>
      </div>
    </div>
    
    <div class="grid-col-4 grid-col-md-12">
      <div class="card">
        <div class="card-header">
          <h2 class="card-title">Alerts & Notifications</h2>
          <div class="card-actions">
            <button class="btn-icon">
              <span class="material-icons">more_vert</span>
            </button>
          </div>
        </div>
        <div class="card-body p-0">
          <AlertsWidget {alerts} />
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .dashboard {
    padding: 16px;
  }
  
  .dashboard-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .dashboard-title {
    font-size: 24px;
    font-weight: 500;
    margin: 0;
    color: var(--dark);
  }
  
  .dashboard-subtitle {
    font-size: 14px;
    color: var(--gray);
    margin: 4px 0 0;
  }
  
  .dashboard-actions {
    display: flex;
    gap: 8px;
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
  
  @media (max-width: 992px) {
    .grid-col-md-12 {
      grid-column: span 12;
    }
    
    .grid-col-md-6 {
      grid-column: span 6;
    }
  }
  
  @media (max-width: 768px) {
    .grid-col-sm-12 {
      grid-column: span 12;
    }
    
    .dashboard-header {
      flex-direction: column;
      align-items: flex-start;
    }
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
  
  .btn-link {
    color: var(--primary);
    text-decoration: none;
    font-size: 14px;
    font-weight: 500;
  }
  
  .btn-icon {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--gray);
    transition: background-color 0.2s ease;
  }
  
  .btn-icon:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
  
  .form-select-sm {
    font-size: 14px;
    padding: 4px 24px 4px 8px;
    height: 32px;
  }
</style>

<!-- No modals for batch form as it's now a separate page -->
