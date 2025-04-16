<script>
  import { onMount } from 'svelte';
  
  // Sample data - in a real app, this would come from an API
  const customers = [
    {
      id: 'C-103',
      name: 'Metro Supermarket',
      contactPerson: 'John Smith',
      email: 'john.smith@metro.com',
      phone: '+1 (555) 123-4567',
      address: '123 Main St, New York, NY 10001',
      type: 'Retailer',
      status: 'active',
      lastOrder: '2023-11-02',
      totalOrders: 28,
      totalSpend: 145000,
      image: 'https://randomuser.me/api/portraits/men/1.jpg'
    },
    {
      id: 'C-102',
      name: 'Family Poultry',
      contactPerson: 'Sarah Johnson',
      email: 'sarah@familypoultry.com',
      phone: '+1 (555) 234-5678',
      address: '456 Oak Ave, Chicago, IL 60007',
      type: 'Wholesaler',
      status: 'active',
      lastOrder: '2023-10-28',
      totalOrders: 42,
      totalSpend: 215000,
      image: 'https://randomuser.me/api/portraits/women/2.jpg'
    },
    {
      id: 'C-101',
      name: 'Green Valley Foods',
      contactPerson: 'Michael Brown',
      email: 'michael@greenvalley.com',
      phone: '+1 (555) 345-6789',
      address: '789 Pine Rd, Los Angeles, CA 90001',
      type: 'Processor',
      status: 'active',
      lastOrder: '2023-11-05',
      totalOrders: 36,
      totalSpend: 180000,
      image: 'https://randomuser.me/api/portraits/men/3.jpg'
    },
    {
      id: 'C-100',
      name: 'Sunshine Markets',
      contactPerson: 'Lisa Davis',
      email: 'lisa@sunshinemarket.com',
      phone: '+1 (555) 456-7890',
      address: '101 Cedar Ln, Miami, FL 33101',
      type: 'Retailer',
      status: 'inactive',
      lastOrder: '2023-09-15',
      totalOrders: 18,
      totalSpend: 85000,
      image: 'https://randomuser.me/api/portraits/women/4.jpg'
    },
    {
      id: 'C-099',
      name: 'Fresh Farms Co-op',
      contactPerson: 'David Wilson',
      email: 'david@freshfarms.org',
      phone: '+1 (555) 567-8901',
      address: '202 Maple Dr, Austin, TX 78701',
      type: 'Wholesaler',
      status: 'active',
      lastOrder: '2023-10-30',
      totalOrders: 24,
      totalSpend: 120000,
      image: 'https://randomuser.me/api/portraits/men/5.jpg'
    }
  ];
  
  const customerStats = {
    totalCustomers: customers.length,
    activeCustomers: customers.filter(c => c.status === 'active').length,
    totalRevenue: customers.reduce((sum, c) => sum + c.totalSpend, 0),
    avgOrderValue: Math.round(customers.reduce((sum, c) => sum + c.totalSpend, 0) / customers.reduce((sum, c) => sum + c.totalOrders, 0))
  };
  
  let filterType = 'all';
  let filterStatus = 'all';
  let searchQuery = '';
  let sortBy = 'name';
  let sortOrder = 'asc';
  
  $: filteredCustomers = customers
    .filter(customer => {
      if (filterType !== 'all' && customer.type !== filterType) return false;
      if (filterStatus !== 'all' && customer.status !== filterStatus) return false;
      if (searchQuery && !customer.name.toLowerCase().includes(searchQuery.toLowerCase()) && 
          !customer.id.toLowerCase().includes(searchQuery.toLowerCase()) &&
          !customer.contactPerson.toLowerCase().includes(searchQuery.toLowerCase())) return false;
      return true;
    })
    .sort((a, b) => {
      const factor = sortOrder === 'asc' ? 1 : -1;
      if (sortBy === 'name') return a.name.localeCompare(b.name) * factor;
      if (sortBy === 'lastOrder') return (new Date(a.lastOrder).getTime() - new Date(b.lastOrder).getTime()) * factor;
      if (sortBy === 'totalSpend') return (a.totalSpend - b.totalSpend) * factor;
      if (sortBy === 'totalOrders') return (a.totalOrders - b.totalOrders) * factor;
      return 0;
    });
    
  function handleSort(field) {
    if (sortBy === field) {
      sortOrder = sortOrder === 'asc' ? 'desc' : 'asc';
    } else {
      sortBy = field;
      sortOrder = 'asc';
    }
  }
  
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
</script>

<svelte:head>
  <title>Customers | BroilerPro</title>
</svelte:head>

<div class="customers-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Customers</h1>
      <p class="page-subtitle">Manage your customer relationships and orders</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-primary">
        <span class="material-icons">person_add</span>
        Add Customer
      </button>
      <button class="btn btn-outline-secondary ms-2">
        <span class="material-icons">file_download</span>
        Export
      </button>
    </div>
  </div>
  
  <div class="stats-summary">
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">groups</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{customerStats.totalCustomers}</h3>
        <p class="stat-label">Total Customers</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">person</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{customerStats.activeCustomers}</h3>
        <p class="stat-label">Active Customers</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">attach_money</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{formatCurrency(customerStats.totalRevenue)}</h3>
        <p class="stat-label">Total Revenue</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">shopping_cart</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{formatCurrency(customerStats.avgOrderValue)}</h3>
        <p class="stat-label">Avg. Order Value</p>
      </div>
    </div>
  </div>
  
  <div class="filter-bar">
    <div class="search-box">
      <span class="material-icons search-icon">search</span>
      <input 
        type="text" 
        placeholder="Search customers..." 
        bind:value={searchQuery}
      />
    </div>
    
    <div class="filter-actions">
      <div class="filter-group">
        <label>Type:</label>
        <select class="form-select" bind:value={filterType}>
          <option value="all">All Types</option>
          <option value="Retailer">Retailer</option>
          <option value="Wholesaler">Wholesaler</option>
          <option value="Processor">Processor</option>
        </select>
      </div>
      
      <div class="filter-group">
        <label>Status:</label>
        <select class="form-select" bind:value={filterStatus}>
          <option value="all">All Statuses</option>
          <option value="active">Active</option>
          <option value="inactive">Inactive</option>
        </select>
      </div>
    </div>
  </div>
  
  <div class="customers-list">
    <div class="card">
      <div class="table-responsive">
        <table class="table table-hover">
          <thead>
            <tr>
              <th>Customer</th>
              <th>
                <button class="sort-header" on:click={() => handleSort('lastOrder')}>
                  Last Order
                  {#if sortBy === 'lastOrder'}
                    <span class="material-icons">
                      {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
                    </span>
                  {/if}
                </button>
              </th>
              <th>
                <button class="sort-header" on:click={() => handleSort('totalOrders')}>
                  Orders
                  {#if sortBy === 'totalOrders'}
                    <span class="material-icons">
                      {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
                    </span>
                  {/if}
                </button>
              </th>
              <th>
                <button class="sort-header" on:click={() => handleSort('totalSpend')}>
                  Total Spend
                  {#if sortBy === 'totalSpend'}
                    <span class="material-icons">
                      {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
                    </span>
                  {/if}
                </button>
              </th>
              <th>Status</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            {#if filteredCustomers.length === 0}
              <tr>
                <td colspan="6" class="text-center py-4">
                  <div class="empty-state">
                    <span class="material-icons">search_off</span>
                    <p>No customers found matching your criteria</p>
                    <button class="btn btn-outline-primary" on:click={() => { filterType = 'all'; filterStatus = 'all'; searchQuery = ''; }}>
                      Clear Filters
                    </button>
                  </div>
                </td>
              </tr>
            {:else}
              {#each filteredCustomers as customer}
                <tr>
                  <td>
                    <div class="customer-info">
                      <div class="customer-avatar">
                        <img src={customer.image} alt={customer.name} />
                      </div>
                      <div class="customer-details">
                        <h4 class="customer-name">{customer.name}</h4>
                        <p class="customer-type">{customer.type} • {customer.id}</p>
                      </div>
                    </div>
                  </td>
                  <td>{formatDate(customer.lastOrder)}</td>
                  <td>{customer.totalOrders}</td>
                  <td>{formatCurrency(customer.totalSpend)}</td>
                  <td>
                    <span class="status-badge status-{customer.status}">
                      {customer.status}
                    </span>
                  </td>
                  <td>
                    <div class="action-buttons">
                      <button class="btn-icon" title="View Details">
                        <span class="material-icons">visibility</span>
                      </button>
                      <button class="btn-icon" title="Edit Customer">
                        <span class="material-icons">edit</span>
                      </button>
                      <button class="btn-icon" title="New Order">
                        <span class="material-icons">add_shopping_cart</span>
                      </button>
                    </div>
                  </td>
                </tr>
              {/each}
            {/if}
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>

<style>
  .customers-page {
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
  
  .stats-summary {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
  }
  
  .stat-card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    display: flex;
    align-items: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .stat-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .stat-icon {
    width: 48px;
    height: 48px;
    border-radius: 8px;
    background-color: rgba(25, 118, 210, 0.1);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .stat-icon .material-icons {
    font-size: 24px;
    color: var(--primary);
  }
  
  .stat-content {
    flex: 1;
  }
  
  .stat-value {
    font-size: 20px;
    font-weight: 500;
    margin: 0 0 4px;
    color: var(--dark);
  }
  
  .stat-label {
    font-size: 12px;
    color: var(--gray);
    margin: 0;
  }
  
  .filter-bar {
    display: flex;
    justify-content: space-between;
    margin-bottom: 16px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .search-box {
    position: relative;
    width: 300px;
  }
  
  .search-icon {
    position: absolute;
    left: 12px;
    top: 50%;
    transform: translateY(-50%);
    color: var(--gray);
    font-size: 20px;
  }
  
  .search-box input {
    width: 100%;
    height: 40px;
    border-radius: 20px;
    border: 1px solid var(--gray-light);
    padding: 0 16px 0 40px;
    font-size: 14px;
    transition: all 0.2s ease;
  }
  
  .search-box input:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 2px rgba(25, 118, 210, 0.2);
  }
  
  .filter-actions {
    display: flex;
    gap: 16px;
    align-items: center;
    flex-wrap: wrap;
  }
  
  .filter-group {
    display: flex;
    align-items: center;
    gap: 8px;
  }
  
  .filter-group label {
    font-size: 14px;
    color: var(--gray);
    margin: 0;
  }
  
  .form-select {
    height: 40px;
    border-radius: 4px;
    border: 1px solid var(--gray-light);
    padding: 0 16px;
    font-size: 14px;
    min-width: 150px;
  }
  
  .customers-list {
    margin-bottom: 24px;
  }
  
  .table {
    margin-bottom: 0;
  }
  
  .table th {
    font-weight: 500;
    color: var(--gray);
    border-top: none;
    padding: 16px;
  }
  
  .sort-header {
    background: none;
    border: none;
    padding: 0;
    font-weight: 500;
    color: var(--gray);
    display: flex;
    align-items: center;
    cursor: pointer;
  }
  
  .sort-header .material-icons {
    font-size: 16px;
    margin-left: 4px;
  }
  
  .table td {
    vertical-align: middle;
    padding: 16px;
  }
  
  .customer-info {
    display: flex;
    align-items: center;
  }
  
  .customer-avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    overflow: hidden;
    margin-right: 12px;
  }
  
  .customer-avatar img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .customer-details {
    flex: 1;
  }
  
  .customer-name {
    font-size: 14px;
    font-weight: 500;
    margin: 0 0 4px;
  }
  
  .customer-type {
    font-size: 12px;
    color: var(--gray);
    margin: 0;
  }
  
  .status-badge {
    display: inline-block;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
    text-transform: capitalize;
  }
  
  .status-active {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .status-inactive {
    background-color: rgba(158, 158, 158, 0.1);
    color: var(--gray);
  }
  
  .action-buttons {
    display: flex;
    gap: 8px;
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
  
  .empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 24px 16px;
    color: var(--gray);
  }
  
  .empty-state .material-icons {
    font-size: 48px;
    margin-bottom: 16px;
    opacity: 0.5;
  }
  
  .empty-state p {
    margin: 0 0 16px;
    font-size: 16px;
  }
  
  @media (max-width: 992px) {
    .stats-summary {
      grid-template-columns: repeat(2, 1fr);
    }
    
    .filter-bar {
      flex-direction: column;
      align-items: stretch;
    }
    
    .search-box {
      width: 100%;
    }
  }
  
  @media (max-width: 768px) {
    .page-header {
      flex-direction: column;
      align-items: flex-start;
    }
    
    .filter-actions {
      flex-direction: column;
      align-items: stretch;
      width: 100%;
    }
    
    .filter-group {
      flex-direction: column;
      align-items: flex-start;
      width: 100%;
    }
    
    .form-select {
      width: 100%;
    }
  }
  
  @media (max-width: 576px) {
    .stats-summary {
      grid-template-columns: 1fr;
    }
  }
</style>
