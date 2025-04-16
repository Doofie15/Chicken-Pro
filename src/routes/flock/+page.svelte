<script>
  import { onMount } from 'svelte';
  import ActiveFlockCard from '../../components/flock/ActiveFlockCard.svelte';
  
  // Modal states for feed and mortality
  let showFeedModal = false;
  let showMortalityModal = false;
  
  // Sample data - in a real app, this would come from an API
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
    },
    {
      id: 'B-2023-43',
      name: 'Batch 43',
      startDate: '2023-09-10',
      age: 63,
      initialCount: 6200,
      currentCount: 6020,
      mortality: 2.9,
      avgWeight: 2480,
      targetWeight: 2500,
      progress: 99,
      feedConversion: 1.68,
      status: 'healthy',
      image: 'https://images.unsplash.com/photo-1563281577-a7be47e20db9?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1470&q=80'
    },
    {
      id: 'B-2023-42',
      name: 'Batch 42',
      startDate: '2023-08-25',
      age: 79,
      initialCount: 5800,
      currentCount: 5630,
      mortality: 2.9,
      avgWeight: 2520,
      targetWeight: 2500,
      progress: 100,
      feedConversion: 1.71,
      status: 'healthy',
      image: 'https://images.unsplash.com/photo-1563281577-a7be47e20db9?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1470&q=80'
    }
  ];
  
  const flockStats = {
    totalBirds: 23870,
    avgAge: 54,
    avgMortality: 2.6,
    avgFCR: 1.69
  };
  
  let filterStatus = 'all';
  let searchQuery = '';
  let sortBy = 'age';
  let sortOrder = 'desc';
  
  $: filteredFlocks = activeFlocks
    .filter(flock => {
      if (filterStatus !== 'all' && flock.status !== filterStatus) return false;
      if (searchQuery && !flock.name.toLowerCase().includes(searchQuery.toLowerCase()) && 
          !flock.id.toLowerCase().includes(searchQuery.toLowerCase())) return false;
      return true;
    })
    .sort((a, b) => {
      const factor = sortOrder === 'asc' ? 1 : -1;
      if (sortBy === 'age') return (a.age - b.age) * factor;
      if (sortBy === 'name') return a.name.localeCompare(b.name) * factor;
      if (sortBy === 'progress') return (a.progress - b.progress) * factor;
      if (sortBy === 'weight') return (a.avgWeight - b.avgWeight) * factor;
      return 0;
    });
    
  function handleSort(field) {
    if (sortBy === field) {
      sortOrder = sortOrder === 'asc' ? 'desc' : 'asc';
    } else {
      sortBy = field;
      sortOrder = 'desc';
    }
  }
</script>

<svelte:head>
  <title>Flock Management | BroilerPro</title>
</svelte:head>

<div class="flock-management">
  <div class="page-header">
    <div>
      <h1 class="page-title">Flock Management</h1>
      <p class="page-subtitle">Manage and monitor all your active flocks</p>
    </div>
    
    <div class="page-actions">
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
  
  <div class="stats-summary">
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">pets</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{flockStats.totalBirds.toLocaleString()}</h3>
        <p class="stat-label">Total Birds</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">calendar_today</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{flockStats.avgAge} days</h3>
        <p class="stat-label">Average Age</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">trending_down</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{flockStats.avgMortality}%</h3>
        <p class="stat-label">Average Mortality</p>
      </div>
    </div>
    
    <div class="stat-card">
      <div class="stat-icon">
        <span class="material-icons">restaurant</span>
      </div>
      <div class="stat-content">
        <h3 class="stat-value">{flockStats.avgFCR}</h3>
        <p class="stat-label">Average FCR</p>
      </div>
    </div>
  </div>
  
  <div class="filter-bar">
    <div class="search-box">
      <span class="material-icons search-icon">search</span>
      <input 
        type="text" 
        placeholder="Search flocks..." 
        bind:value={searchQuery}
      />
    </div>
    
    <div class="filter-actions">
      <div class="filter-group">
        <label>Status:</label>
        <select class="form-select" bind:value={filterStatus}>
          <option value="all">All Statuses</option>
          <option value="healthy">Healthy</option>
          <option value="warning">Warning</option>
          <option value="danger">Danger</option>
        </select>
      </div>
      
      <div class="filter-group">
        <label>Sort by:</label>
        <div class="sort-buttons">
          <button 
            class="sort-btn" 
            class:active={sortBy === 'age'} 
            on:click={() => handleSort('age')}
          >
            Age
            {#if sortBy === 'age'}
              <span class="material-icons">
                {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
              </span>
            {/if}
          </button>
          
          <button 
            class="sort-btn" 
            class:active={sortBy === 'progress'} 
            on:click={() => handleSort('progress')}
          >
            Progress
            {#if sortBy === 'progress'}
              <span class="material-icons">
                {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
              </span>
            {/if}
          </button>
          
          <button 
            class="sort-btn" 
            class:active={sortBy === 'weight'} 
            on:click={() => handleSort('weight')}
          >
            Weight
            {#if sortBy === 'weight'}
              <span class="material-icons">
                {sortOrder === 'asc' ? 'arrow_upward' : 'arrow_downward'}
              </span>
            {/if}
          </button>
        </div>
      </div>
    </div>
  </div>
  
  <div class="flock-list">
    <div class="card">
      {#if filteredFlocks.length === 0}
        <div class="empty-state">
          <span class="material-icons">search_off</span>
          <p>No flocks found matching your criteria</p>
          <button class="btn btn-outline-primary" on:click={() => { filterStatus = 'all'; searchQuery = ''; }}>
            Clear Filters
          </button>
        </div>
      {:else}
        {#each filteredFlocks as flock}
          <ActiveFlockCard {flock} />
        {/each}
      {/if}
    </div>
  </div>
</div>

<style>
  .flock-management {
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
  
  .sort-buttons {
    display: flex;
    gap: 8px;
  }
  
  .sort-btn {
    height: 40px;
    border-radius: 4px;
    border: 1px solid var(--gray-light);
    background-color: white;
    padding: 0 12px;
    font-size: 14px;
    display: flex;
    align-items: center;
    gap: 4px;
    cursor: pointer;
    transition: all 0.2s ease;
  }
  
  .sort-btn:hover {
    border-color: var(--primary);
  }
  
  .sort-btn.active {
    background-color: var(--primary);
    border-color: var(--primary);
    color: white;
  }
  
  .sort-btn .material-icons {
    font-size: 16px;
  }
  
  .flock-list {
    margin-bottom: 24px;
  }
  
  .empty-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 48px 16px;
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
  
  @media (max-width: 576px) {
    .stats-summary {
      grid-template-columns: 1fr;
    }
    
    .page-header {
      flex-direction: column;
      align-items: flex-start;
    }
    
    .filter-actions {
      flex-direction: column;
      align-items: stretch;
    }
    
    .filter-group {
      flex-direction: column;
      align-items: flex-start;
    }
    
    .sort-buttons {
      flex-wrap: wrap;
    }
  }
</style>

<!-- No modals for batch form as it's now a separate page -->
