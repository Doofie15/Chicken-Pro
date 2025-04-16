<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';
  import BatchForm from '../../../components/flock/BatchForm.svelte';
  
  // Handle form submission
  function handleSubmit(event) {
    const batchData = event.detail;
    console.log('New batch created:', batchData);
    
    // In a real app, this would send the data to an API
    // and update the active flocks list
    
    // Show success message
    alert(`Batch ${batchData.name} created successfully!`);
    
    // Navigate back to flock management page
    goto('/flock');
  }
  
  // Handle form cancellation
  function handleCancel() {
    // Navigate back to flock management page
    goto('/flock');
  }
</script>

<svelte:head>
  <title>Add New Batch | BroilerPro</title>
</svelte:head>

<div class="add-batch-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Add New Batch</h1>
      <p class="page-subtitle">Enter details for the new broiler batch</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-outline-secondary" on:click={handleCancel}>
        <span class="material-icons">arrow_back</span>
        Back to Flock Management
      </button>
    </div>
  </div>
  
  <div class="page-content">
    <BatchForm 
      on:submit={handleSubmit} 
      on:cancel={handleCancel} 
    />
  </div>
</div>

<style>
  .add-batch-page {
    padding: 16px;
  }
  
  .page-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
  }
  
  .page-title {
    margin: 0;
    font-size: 24px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .page-subtitle {
    margin: 4px 0 0;
    color: var(--gray);
  }
  
  .page-actions {
    display: flex;
    gap: 8px;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    padding: 8px 16px;
    border-radius: 4px;
    font-weight: 500;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
  }
  
  .btn .material-icons {
    font-size: 18px;
    margin-right: 8px;
  }
  
  .btn-outline-secondary {
    background-color: transparent;
    border: 1px solid #ddd;
    color: var(--gray);
  }
  
  .btn-outline-secondary:hover {
    background-color: #f5f5f5;
  }
  
  .page-content {
    max-width: 1000px;
    margin: 0 auto;
  }
</style>
