<script>
  import { page } from '$app/stores';
  
  export let open = true;
  
  const menuItems = [
    { 
      label: 'Dashboard', 
      icon: 'dashboard', 
      path: '/' 
    },
    { 
      label: 'Flock Management', 
      icon: 'pets', 
      path: '/flock'
    },
    { 
      label: 'Customers', 
      icon: 'people', 
      path: '/customers'
    },
    { 
      label: 'Finances', 
      icon: 'attach_money', 
      path: '/finances'
    },
    { 
      label: 'Analytics', 
      icon: 'insights', 
      path: '/analytics'
    },
    { 
      label: 'Tasks', 
      icon: 'task_alt', 
      path: '/tasks' 
    },
    { 
      label: 'Settings', 
      icon: 'settings', 
      path: '/settings' 
    }
  ];
  
  let expandedItems = {};
  
  function toggleSubMenu(index) {
    expandedItems[index] = !expandedItems[index];
  }
  
  $: currentPath = $page.url.pathname;
</script>

<aside class="sidebar" class:open>
  <div class="sidebar-header">
    <div class="logo-container">
      <span class="material-icons logo-icon">egg</span>
      <h2 class="logo-text">BroilerPro</h2>
    </div>
  </div>
  
  <nav class="sidebar-nav">
    <ul class="nav-list">
      {#each menuItems as item, index}
        <li class="nav-item" class:active={currentPath === item.path || currentPath.startsWith(item.path + '/')}>
          <a 
            href={item.path} 
            class="nav-link" 
          >
            <span class="material-icons nav-icon">{item.icon}</span>
            <span class="nav-text">{item.label}</span>

          </a>
          

        </li>
      {/each}
    </ul>
  </nav>
  
  <div class="sidebar-footer">
    <div class="user-info">
      <div class="user-avatar">
        <span class="material-icons">account_circle</span>
      </div>
      <div class="user-details">
        <p class="user-name">Farm Manager</p>
        <p class="user-role">Administrator</p>
      </div>
    </div>
  </div>
</aside>

<style>
  .sidebar {
    width: 250px;
    height: 100vh;
    background-color: white;
    box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1000;
    transform: translateX(-100%);
    transition: transform 0.3s ease;
  }
  
  .sidebar.open {
    transform: translateX(0);
  }
  
  .sidebar-header {
    padding: 20px;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .logo-container {
    display: flex;
    align-items: center;
  }
  
  .logo-icon {
    font-size: 28px;
    color: var(--primary);
    margin-right: 10px;
  }
  
  .logo-text {
    font-size: 20px;
    font-weight: 500;
    color: var(--primary);
    margin: 0;
  }
  
  .sidebar-nav {
    flex: 1;
    overflow-y: auto;
    padding: 10px 0;
  }
  
  .nav-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  
  .nav-item {
    margin: 2px 0;
  }
  
  .nav-link {
    display: flex;
    align-items: center;
    padding: 12px 20px;
    color: var(--dark);
    text-decoration: none;
    transition: background-color 0.2s ease;
    border-radius: 4px;
    margin: 0 8px;
  }
  
  .nav-link:hover {
    background-color: rgba(25, 118, 210, 0.08);
  }
  
  .nav-item.active .nav-link {
    background-color: rgba(25, 118, 210, 0.12);
    color: var(--primary);
  }
  
  .nav-icon {
    margin-right: 12px;
    font-size: 20px;
  }
  
  .expand-icon {
    margin-left: auto;
    font-size: 20px;
  }
  
  .sub-nav-list {
    list-style: none;
    padding-left: 56px;
    margin: 0;
    overflow: hidden;
  }
  
  .sub-nav-link {
    display: block;
    padding: 8px 0;
    color: var(--gray);
    text-decoration: none;
    font-size: 14px;
    transition: color 0.2s ease;
  }
  
  .sub-nav-link:hover {
    color: var(--primary);
  }
  
  .sub-nav-item.active .sub-nav-link {
    color: var(--primary);
    font-weight: 500;
  }
  
  .sidebar-footer {
    padding: 16px;
    border-top: 1px solid var(--gray-light);
  }
  
  .user-info {
    display: flex;
    align-items: center;
  }
  
  .user-avatar {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    background-color: var(--gray-light);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 12px;
  }
  
  .user-avatar .material-icons {
    font-size: 24px;
    color: var(--gray);
  }
  
  .user-name {
    margin: 0;
    font-size: 14px;
    font-weight: 500;
  }
  
  .user-role {
    margin: 0;
    font-size: 12px;
    color: var(--gray);
  }
</style>
