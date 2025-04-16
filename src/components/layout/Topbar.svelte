<script>
  export let toggleSidebar;
  export let isMobile;
  
  let searchQuery = '';
  let showNotifications = false;
  let notifications = [
    {
      id: 1,
      type: 'warning',
      message: 'Flock #B-2023-45 mortality rate above threshold',
      time: '10 minutes ago'
    },
    {
      id: 2,
      type: 'info',
      message: 'New order placed by Customer #C-103',
      time: '1 hour ago'
    },
    {
      id: 3,
      type: 'success',
      message: 'Flock #B-2023-42 reached target weight',
      time: '3 hours ago'
    },
    {
      id: 4,
      type: 'danger',
      message: 'Feed inventory low - reorder required',
      time: '5 hours ago'
    }
  ];
  
  function toggleNotifications() {
    showNotifications = !showNotifications;
  }
  
  function handleSearch() {
    // Implement search functionality
    console.log('Searching for:', searchQuery);
  }
</script>

<header class="topbar">
  <div class="topbar-left">
    <button class="menu-toggle" on:click={toggleSidebar}>
      <span class="material-icons">menu</span>
    </button>
    
    {#if isMobile}
      <div class="mobile-logo">
        <span class="material-icons logo-icon">egg</span>
        <span class="logo-text">BroilerPro</span>
      </div>
    {/if}
  </div>
  
  <div class="search-container">
    <div class="search-box">
      <span class="material-icons search-icon">search</span>
      <input 
        type="text" 
        placeholder="Search..." 
        bind:value={searchQuery}
        on:keydown={(e) => e.key === 'Enter' && handleSearch()}
      />
    </div>
  </div>
  
  <div class="topbar-right">
    <div class="topbar-actions">
      <button class="action-button">
        <span class="material-icons">add</span>
        <span class="action-text">New Task</span>
      </button>
      
      <div class="notification-container">
        <button class="icon-button" on:click={toggleNotifications}>
          <span class="material-icons">notifications</span>
          <span class="notification-badge">{notifications.length}</span>
        </button>
        
        {#if showNotifications}
          <div class="notification-dropdown">
            <div class="notification-header">
              <h3>Notifications</h3>
              <button class="text-button">Mark all as read</button>
            </div>
            
            <div class="notification-list">
              {#each notifications as notification}
                <div class="notification-item" class:unread={true}>
                  <div class="notification-icon {notification.type}">
                    <span class="material-icons">
                      {#if notification.type === 'warning'}
                        warning
                      {:else if notification.type === 'info'}
                        info
                      {:else if notification.type === 'success'}
                        check_circle
                      {:else if notification.type === 'danger'}
                        error
                      {/if}
                    </span>
                  </div>
                  <div class="notification-content">
                    <p class="notification-message">{notification.message}</p>
                    <p class="notification-time">{notification.time}</p>
                  </div>
                </div>
              {/each}
            </div>
            
            <div class="notification-footer">
              <a href="/notifications" class="view-all">View all notifications</a>
            </div>
          </div>
        {/if}
      </div>
      
      <button class="icon-button">
        <span class="material-icons">help_outline</span>
      </button>
    </div>
  </div>
</header>

<style>
  .topbar {
    height: 64px;
    background-color: white;
    border-bottom: 1px solid var(--gray-light);
    display: flex;
    align-items: center;
    padding: 0 16px;
    position: sticky;
    top: 0;
    z-index: 900;
  }
  
  .topbar-left {
    display: flex;
    align-items: center;
  }
  
  .menu-toggle {
    background: none;
    border: none;
    cursor: pointer;
    width: 40px;
    height: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    transition: background-color 0.2s ease;
  }
  
  .menu-toggle:hover {
    background-color: rgba(0, 0, 0, 0.05);
  }
  
  .mobile-logo {
    display: flex;
    align-items: center;
    margin-left: 8px;
  }
  
  .logo-icon {
    font-size: 24px;
    color: var(--primary);
    margin-right: 8px;
  }
  
  .logo-text {
    font-size: 18px;
    font-weight: 500;
    color: var(--primary);
  }
  
  .search-container {
    flex: 1;
    display: flex;
    justify-content: center;
    padding: 0 16px;
  }
  
  .search-box {
    max-width: 500px;
    width: 100%;
    position: relative;
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
  
  .topbar-right {
    display: flex;
    align-items: center;
  }
  
  .topbar-actions {
    display: flex;
    align-items: center;
    gap: 8px;
  }
  
  .action-button {
    display: flex;
    align-items: center;
    background-color: var(--primary);
    color: white;
    border: none;
    border-radius: 20px;
    padding: 8px 16px;
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    transition: background-color 0.2s ease;
  }
  
  .action-button:hover {
    background-color: var(--primary-dark);
  }
  
  .action-button .material-icons {
    font-size: 18px;
    margin-right: 8px;
  }
  
  .icon-button {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    cursor: pointer;
    position: relative;
    transition: background-color 0.2s ease;
  }
  
  .icon-button:hover {
    background-color: rgba(0, 0, 0, 0.05);
  }
  
  .notification-badge {
    position: absolute;
    top: 4px;
    right: 4px;
    background-color: var(--danger);
    color: white;
    font-size: 10px;
    font-weight: 500;
    min-width: 16px;
    height: 16px;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0 4px;
  }
  
  .notification-container {
    position: relative;
  }
  
  .notification-dropdown {
    position: absolute;
    top: 48px;
    right: 0;
    width: 320px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    z-index: 1000;
    overflow: hidden;
  }
  
  .notification-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .notification-header h3 {
    margin: 0;
    font-size: 16px;
    font-weight: 500;
  }
  
  .text-button {
    background: none;
    border: none;
    color: var(--primary);
    font-size: 12px;
    cursor: pointer;
    padding: 0;
  }
  
  .notification-list {
    max-height: 320px;
    overflow-y: auto;
  }
  
  .notification-item {
    display: flex;
    padding: 12px 16px;
    border-bottom: 1px solid var(--gray-light);
    transition: background-color 0.2s ease;
  }
  
  .notification-item:hover {
    background-color: rgba(0, 0, 0, 0.02);
  }
  
  .notification-item.unread {
    background-color: rgba(25, 118, 210, 0.05);
  }
  
  .notification-icon {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 12px;
  }
  
  .notification-icon.warning {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .notification-icon.info {
    background-color: rgba(33, 150, 243, 0.1);
    color: var(--info);
  }
  
  .notification-icon.success {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .notification-icon.danger {
    background-color: rgba(244, 67, 54, 0.1);
    color: var(--danger);
  }
  
  .notification-content {
    flex: 1;
  }
  
  .notification-message {
    margin: 0 0 4px;
    font-size: 14px;
  }
  
  .notification-time {
    margin: 0;
    font-size: 12px;
    color: var(--gray);
  }
  
  .notification-footer {
    padding: 12px 16px;
    text-align: center;
    border-top: 1px solid var(--gray-light);
  }
  
  .view-all {
    color: var(--primary);
    text-decoration: none;
    font-size: 14px;
    font-weight: 500;
  }
  
  @media (max-width: 767px) {
    .action-text {
      display: none;
    }
    
    .action-button {
      padding: 8px;
    }
    
    .action-button .material-icons {
      margin-right: 0;
    }
    
    .search-container {
      display: none;
    }
  }
</style>
