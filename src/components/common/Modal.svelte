<script>
  import { createEventDispatcher, onMount } from 'svelte';
  
  export let show = false;
  export let title = '';
  export let size = 'medium'; // small, medium, large, fullscreen
  export let closeOnBackdropClick = true;
  
  const dispatch = createEventDispatcher();
  
  function close() {
    dispatch('close');
  }
  
  function handleBackdropClick(event) {
    if (closeOnBackdropClick && event.target === event.currentTarget) {
      close();
    }
  }
  
  function handleKeydown(event) {
    if (event.key === 'Escape') {
      close();
    }
  }
  
  onMount(() => {
    document.addEventListener('keydown', handleKeydown);
    
    return () => {
      document.removeEventListener('keydown', handleKeydown);
    };
  });
</script>

{#if show}
  <div class="modal-backdrop" on:click={handleBackdropClick}>
    <div class="modal-container {size}">
      <div class="modal-header">
        <h2 class="modal-title">{title}</h2>
        <button class="modal-close" on:click={close}>
          <span class="material-icons">close</span>
        </button>
      </div>
      <div class="modal-body">
        <slot></slot>
      </div>
    </div>
  </div>
{/if}

<style>
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
    padding: 20px;
    overflow-y: auto;
  }
  
  .modal-container {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
    display: flex;
    flex-direction: column;
    max-height: calc(100vh - 40px);
    width: 100%;
    overflow: hidden;
  }
  
  .modal-container.small {
    max-width: 400px;
  }
  
  .modal-container.medium {
    max-width: 600px;
  }
  
  .modal-container.large {
    max-width: 900px;
  }
  
  .modal-container.fullscreen {
    max-width: 1200px;
    height: calc(100vh - 40px);
  }
  
  .modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 20px;
    border-bottom: 1px solid #eee;
  }
  
  .modal-title {
    margin: 0;
    font-size: 18px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .modal-close {
    background: none;
    border: none;
    cursor: pointer;
    color: var(--gray);
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 4px;
    border-radius: 4px;
    transition: background-color 0.2s;
  }
  
  .modal-close:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
  
  .modal-close .material-icons {
    font-size: 20px;
  }
  
  .modal-body {
    padding: 0;
    overflow-y: auto;
    flex: 1;
  }
  
  @media (max-width: 768px) {
    .modal-container.small,
    .modal-container.medium,
    .modal-container.large {
      max-width: 100%;
    }
  }
</style>
