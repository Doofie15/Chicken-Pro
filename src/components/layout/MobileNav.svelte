<script>
  import { page } from '$app/stores';
  
  const navItems = [
    { icon: 'dashboard', label: 'Dashboard', path: '/' },
    { icon: 'pets', label: 'Flocks', path: '/flock' },
    { icon: 'people', label: 'Customers', path: '/customers' },
    { icon: 'attach_money', label: 'Finances', path: '/finances' },
    { icon: 'menu', label: 'More', path: '#more' }
  ];
  
  let showMoreMenu = false;
  
  const moreItems = [
    { icon: 'insights', label: 'Analytics', path: '/analytics' },
    { icon: 'task_alt', label: 'Tasks', path: '/tasks' },
    { icon: 'settings', label: 'Settings', path: '/settings' }
  ];
  
  function toggleMoreMenu() {
    showMoreMenu = !showMoreMenu;
  }
  
  $: currentPath = $page.url.pathname;
</script>

<nav class="mobile-nav">
  <div class="nav-container">
    {#each navItems as item}
      {#if item.path === '#more'}
        <button 
          class="nav-item" 
          class:active={showMoreMenu}
          on:click={toggleMoreMenu}
        >
          <span class="material-icons">{item.icon}</span>
          <span class="nav-label">{item.label}</span>
        </button>
      {:else}
        <a 
          href={item.path} 
          class="nav-item" 
          class:active={currentPath === item.path || currentPath.startsWith(item.path + '/')}
        >
          <span class="material-icons">{item.icon}</span>
          <span class="nav-label">{item.label}</span>
        </a>
      {/if}
    {/each}
  </div>
  
  {#if showMoreMenu}
    <div class="more-menu">
      {#each moreItems as item}
        <a 
          href={item.path} 
          class="more-item" 
          class:active={currentPath === item.path || currentPath.startsWith(item.path + '/')}
        >
          <span class="material-icons">{item.icon}</span>
          <span class="more-label">{item.label}</span>
        </a>
      {/each}
    </div>
  {/if}
</nav>

<style>
  .mobile-nav {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    background-color: white;
    box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.1);
    z-index: 1000;
  }
  
  .nav-container {
    display: flex;
    height: 60px;
  }
  
  .nav-item {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: var(--gray);
    text-decoration: none;
    transition: color 0.2s ease;
    background: none;
    border: none;
    cursor: pointer;
    padding: 8px 0;
  }
  
  .nav-item:hover, .nav-item.active {
    color: var(--primary);
  }
  
  .nav-item .material-icons {
    font-size: 24px;
    margin-bottom: 4px;
  }
  
  .nav-label {
    font-size: 12px;
  }
  
  .more-menu {
    position: absolute;
    bottom: 70px;
    right: 10px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    overflow: hidden;
    width: 160px;
  }
  
  .more-item {
    display: flex;
    align-items: center;
    padding: 12px 16px;
    color: var(--dark);
    text-decoration: none;
    transition: background-color 0.2s ease;
  }
  
  .more-item:hover, .more-item.active {
    background-color: rgba(25, 118, 210, 0.08);
    color: var(--primary);
  }
  
  .more-item .material-icons {
    margin-right: 12px;
    font-size: 20px;
  }
  
  .more-label {
    font-size: 14px;
  }
</style>
