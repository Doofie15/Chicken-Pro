<script>
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';
  import CustomerForm from '../../../components/customers/CustomerForm.svelte';
  
  // Handle form submission
  function handleSubmit(event) {
    const customerData = event.detail;
    console.log('New customer created:', customerData);
    
    // In a real app, this would send the data to an API
    // and update the customers list
    
    // Show success message
    alert(`Customer ${customerData.name} created successfully!`);
    
    // Navigate back to customers page
    goto('/customers');
  }
  
  // Handle form cancellation
  function handleCancel() {
    // Navigate back to customers page
    goto('/customers');
  }
  
  // Load Google Maps API script
  onMount(() => {
    // In a real app, you would load the Google Maps API here with your API key
    // This is just a placeholder - you would need a valid API key
    /*
    const script = document.createElement('script');
    script.src = `https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places`;
    script.async = true;
    script.defer = true;
    document.head.appendChild(script);
    */
  });
</script>

<svelte:head>
  <title>Add New Customer | BroilerPro</title>
</svelte:head>

<div class="add-customer-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Add New Customer</h1>
      <p class="page-subtitle">Enter details for the new customer</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-outline-secondary" on:click={handleCancel}>
        <span class="material-icons">arrow_back</span>
        Back to Customers
      </button>
    </div>
  </div>
  
  <div class="page-content">
    <CustomerForm 
      on:submit={handleSubmit} 
      on:cancel={handleCancel} 
    />
  </div>
</div>

<style>
  .add-customer-page {
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
