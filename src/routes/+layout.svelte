<script>
  import { onMount } from 'svelte';
  import '../app.css';
  import Sidebar from '../components/layout/Sidebar.svelte';
  import Topbar from '../components/layout/Topbar.svelte';
  import MobileNav from '../components/layout/MobileNav.svelte';
  
  let isMobile = false;
  let sidebarOpen = true;
  
  function toggleSidebar() {
    sidebarOpen = !sidebarOpen;
  }
  
  onMount(() => {
    const checkMobile = () => {
      isMobile = window.innerWidth < 768;
      if (isMobile) sidebarOpen = false;
    };
    
    checkMobile();
    window.addEventListener('resize', checkMobile);
    
    return () => {
      window.removeEventListener('resize', checkMobile);
    };
  });
</script>

<div class="app-container">
  {#if !isMobile}
    <Sidebar open={sidebarOpen} />
  {/if}
  
  <div class="main-content" class:sidebar-open={sidebarOpen && !isMobile}>
    <Topbar {toggleSidebar} {isMobile} />
    
    <main>
      <slot />
    </main>
  </div>
  
  {#if isMobile}
    <MobileNav />
  {/if}
</div>

<style>
  .app-container {
    display: flex;
    height: 100vh;
    width: 100%;
    overflow: hidden;
    background-color: #f8f9fa;
  }
  
  .main-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    transition: margin-left 0.3s ease;
    width: 100%;
  }
  
  .sidebar-open {
    margin-left: 250px;
  }
  
  main {
    flex: 1;
    overflow-y: auto;
    padding: 20px;
  }
  
  @media (max-width: 767px) {
    .main-content {
      margin-left: 0 !important;
    }
    
    main {
      padding-bottom: 70px; /* Space for mobile nav */
    }
  }
</style>
