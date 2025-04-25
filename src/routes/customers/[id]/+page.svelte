<script lang="ts">
    import { onMount } from 'svelte';
    import { goto } from '$app/navigation';
    import { page } from '$app/stores';
    
    // Customer data type
    type Customer = {
      id: string;
      name: string;
      contactPerson: string;
      email: string;
      phone: string;
      address: string;
      type: string;
      status: string;
      lastOrder: string;
      totalOrders: number;
      totalSpend: number;
      image: string;
      vatNumber?: string;
      registrationNumber?: string;
      createdAt?: string;
      creditLimit?: number;
      paymentTerms?: string;
      preferredPaymentMethod?: string;
      notes?: string;
    };
    
    // Edit state trackers
    let editingContact = false;
    let editingBusiness = false;
    let editingNotes = false;
    
    // Form data for editing
    let editContactData = {
      contactPerson: '',
      email: '',
      phone: '',
      address: ''
    };
    
    let editBusinessData = {
      vatNumber: '',
      registrationNumber: '',
      paymentTerms: ''
    };
    
    let editNotesData = {
      notes: ''
    };
    
    // Sample customer data - in a real app, this would come from an API
    let customer: Customer | null = null;
    let loading = true;
    let error = false;
    
    // Tab state
    let activeTab = 'overview';
    
    // Recent orders data
    const recentOrders = [
      { id: 'ORD-2023-001', date: '2023-11-02', amount: 12500, status: 'delivered' },
      { id: 'ORD-2023-002', date: '2023-10-15', amount: 8750, status: 'processing' },
      { id: 'ORD-2023-003', date: '2023-09-28', amount: 15000, status: 'delivered' },
      { id: 'ORD-2023-004', date: '2023-09-10', amount: 9250, status: 'delivered' }
    ];
    
    // Payment history data
    const paymentHistory = [
      { id: 'PMT-2023-001', date: '2023-11-05', amount: 12500, method: 'bank_transfer', status: 'completed' },
      { id: 'PMT-2023-002', date: '2023-10-18', amount: 8750, method: 'credit_card', status: 'completed' },
      { id: 'PMT-2023-003', date: '2023-09-30', amount: 15000, method: 'bank_transfer', status: 'completed' },
      { id: 'PMT-2023-004', date: '2023-09-12', amount: 9250, method: 'credit_card', status: 'completed' }
    ];
    
    // Delivery history data
    const deliveryHistory = [
      { id: 'DEL-2023-001', date: '2023-11-03', items: 450, status: 'delivered', driver: 'Michael Johnson' },
      { id: 'DEL-2023-002', date: '2023-10-17', items: 320, status: 'delivered', driver: 'Sarah Williams' },
      { id: 'DEL-2023-003', date: '2023-09-29', items: 580, status: 'delivered', driver: 'Michael Johnson' },
      { id: 'DEL-2023-004', date: '2023-09-11', items: 410, status: 'delivered', driver: 'David Brown' }
    ];
    
    // Documents data
    let documents = [
      { id: 'DOC-001', name: 'Contract Agreement', type: 'contract', date: '2023-01-15', size: '2.4 MB' },
      { id: 'DOC-002', name: 'Tax Certificate', type: 'certificate', date: '2023-02-20', size: '1.1 MB' },
      { id: 'DOC-003', name: 'Company Registration', type: 'legal', date: '2023-01-10', size: '3.2 MB' },
      { id: 'DOC-004', name: 'Credit Application', type: 'financial', date: '2023-01-18', size: '1.8 MB' }
    ];
    
    // Communication logs
    const communicationLogs = [
      { id: 'COM-001', date: '2023-11-10', type: 'email', subject: 'Order Confirmation', contact: 'John Smith' },
      { id: 'COM-002', date: '2023-10-28', type: 'call', subject: 'Delivery Schedule', contact: 'John Smith' },
      { id: 'COM-003', date: '2023-10-15', type: 'email', subject: 'Invoice #INV-2023-002', contact: 'Sarah Johnson' },
      { id: 'COM-004', date: '2023-09-30', type: 'meeting', subject: 'Quarterly Review', contact: 'John Smith' }
    ];
    
    // Map functionality
    let mapLoaded = false;
    let map: any = null;
    let marker: any = null;
    let mapElement: HTMLElement | null = null;
    let googleMapsLoaded = false;
    
    // Alternative addresses
    let alternativeAddresses: {id: string, label: string, address: string, isPrimary: boolean}[] = [
      // Will be populated if the customer has alternative addresses
    ];
    
    // Financial data
    let editingFinancial = false;
    let editFinancialData = {
      creditLimit: 0,
      preferredPaymentMethod: ''
    };
    
    // Document upload
    let uploadingDocument = false;
    let newDocument = {
      name: '',
      type: 'contract',
      file: null as File | null
    };
    
    // New address form
    let addingAddress = false;
    let newAddressData = {
      label: '',
      address: ''
    };
    
    // Filter states
    let activityFilter = 'all';
    let orderStatusFilter = 'all';
    let orderDateFilter = 'all';
    let documentTypeFilter = 'all';
    let deliveryStatusFilter = 'all';
    
    // Format currency
    function formatCurrency(value: number) {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'ZAR',
        minimumFractionDigits: 0
      }).format(value);
    }
    
    // Format date
    function formatDate(dateString: string) {
      const date = new Date(dateString);
      return new Intl.DateTimeFormat('en-US', { 
        month: 'short', 
        day: 'numeric',
        year: 'numeric'
      }).format(date);
    }
    
    // Fetch customer data
    onMount(async () => {
      try {
        // In a real app, this would be an API call
        // const response = await fetch(`/api/customers/${$page.params.id}`);
        // customer = await response.json();
        
        // For now, we'll use sample data
        setTimeout(() => {
          customer = {
            id: $page.params.id,
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
            image: 'https://randomuser.me/api/portraits/men/1.jpg',
            vatNumber: 'VAT12345678',
            registrationNumber: 'REG987654321',
            createdAt: '2022-05-15',
            creditLimit: 250000,
            paymentTerms: 'Net 30',
            preferredPaymentMethod: 'Bank Transfer',
            notes: 'Large retail chain with multiple locations. Prefers deliveries on weekdays before noon.'
          };
          loading = false;
        }, 800);
      } catch (err) {
        error = true;
        loading = false;
      }
    });
    
    // Simple tab change function
    function setActiveTab(tabId: string) {
      activeTab = tabId;
      
      // Initialize map when delivery tab is selected
      if (tabId === 'delivery' && !mapLoaded) {
        setTimeout(() => {
          mapElement = document.getElementById('map');
          if (mapElement && !mapLoaded) {
            initMap();
          }
        }, 100);
      }
    }
    
    // Toggle edit mode for contact information
    function toggleEditContact() {
      if (!editingContact) {
        // Initialize form with current data
        editContactData = {
          contactPerson: customer?.contactPerson || '',
          email: customer?.email || '',
          phone: customer?.phone || '',
          address: customer?.address || ''
        };
      }
      editingContact = !editingContact;
    }
    
    // Toggle edit mode for business information
    function toggleEditBusiness() {
      if (!editingBusiness) {
        // Initialize form with current data
        editBusinessData = {
          vatNumber: customer?.vatNumber || '',
          registrationNumber: customer?.registrationNumber || '',
          paymentTerms: customer?.paymentTerms || ''
        };
      }
      editingBusiness = !editingBusiness;
    }
    
    // Toggle edit mode for notes
    function toggleEditNotes() {
      if (!editingNotes) {
        // Initialize form with current data
        editNotesData = {
          notes: customer?.notes || ''
        };
      }
      editingNotes = !editingNotes;
    }
    
    // Save contact information
    function saveContactInfo() {
      if (customer) {
        customer.contactPerson = editContactData.contactPerson;
        customer.email = editContactData.email;
        customer.phone = editContactData.phone;
        customer.address = editContactData.address;
        // In a real app, this would be an API call to update the data
        // Example: await fetch(`/api/customers/${customer.id}`, {
        //   method: 'PATCH',
        //   body: JSON.stringify(editContactData)
        // });
      }
      editingContact = false;
    }
    
    // Save business information
    function saveBusinessInfo() {
      if (customer) {
        customer.vatNumber = editBusinessData.vatNumber;
        customer.registrationNumber = editBusinessData.registrationNumber;
        customer.paymentTerms = editBusinessData.paymentTerms;
        // In a real app, this would be an API call to update the data
      }
      editingBusiness = false;
    }
    
    // Save notes
    function saveNotes() {
      if (customer) {
        customer.notes = editNotesData.notes;
        // In a real app, this would be an API call to update the data
      }
      editingNotes = false;
    }
    
    // View order details
    function viewOrder(orderId: string) {
      // In a real app, this would navigate to the order details page
      alert(`Viewing order ${orderId}`);
      // Example: goto(`/orders/${orderId}`);
    }
    
    // Download invoice
    function downloadInvoice(orderId: string) {
      // In a real app, this would download the invoice file
      alert(`Downloading invoice for order ${orderId}`);
      // Example: window.open(`/api/invoices/${orderId}/download`, '_blank');
    }
    
    // Initialize map when the delivery tab is activated
    // Load Google Maps API
    function loadGoogleMapsAPI() {
      return new Promise((resolve, reject) => {
        if (typeof window !== 'undefined' && window.document) {
          // Skip if already loaded
          if (typeof window !== 'undefined' && 'google' in window) {
            googleMapsLoaded = true;
            resolve(window);
            return;
          }

          const script = document.createElement('script');
          const apiKey = 'AIzaSyD3wWF2a8TwWnXG7W_8ALtydF1si4JCpOY';
          script.src = `https://maps.googleapis.com/maps/api/js?key=${apiKey}&libraries=places`;
          script.async = true;
          script.defer = true;

          script.onload = () => {
            googleMapsLoaded = true;
            resolve(window);
          };

          script.onerror = (error) => reject(new Error(`Google Maps API failed to load: ${error}`));

          document.head.appendChild(script);
        }
      });
    }

    // Initialize map
    async function initMap() {
      if (typeof window !== 'undefined' && !mapLoaded && customer && mapElement) {
        try {
          // Load Google Maps API if not already loaded
          if (!googleMapsLoaded) {
            await loadGoogleMapsAPI();
          }

          mapLoaded = true;
          const google = (window as any).google;

          // Get coordinates - in a real app you would use geocoding
          // For now we'll use fixed coordinates as an example
          const defaultLat = -33.9249; // Default coordinates (can be replaced with geocoding)
          const defaultLng = 18.4241;

          // Create the map centered at the customer address
          map = new google.maps.Map(mapElement, {
            center: { lat: defaultLat, lng: defaultLng },
            zoom: 15,
            mapTypeControl: false,
            streetViewControl: false,
            fullscreenControl: true,
            zoomControl: true,
          });

          // Add a marker for the customer's location
          marker = new google.maps.Marker({
            position: { lat: defaultLat, lng: defaultLng },
            map: map,
            title: customer?.name || '',
            animation: google.maps.Animation.DROP
          });

          // Add info window with customer address
          const infowindow = new google.maps.InfoWindow({
            content: `<div class="map-info-window"><strong>${customer?.name || ''}</strong><br>${customer?.address || ''}</div>`
          });

          marker.addListener('click', () => {
            infowindow.open(map, marker);
          });
          
          // Open info window by default
          infowindow.open(map, marker);
        } catch (error) {
          console.error('Error initializing map:', error);
          const mapElement = document.getElementById('map');
          if (mapElement) {
            mapElement.innerHTML = `
              <div class="map-error">
                <span class="material-icons map-icon">error</span>
                <p>Unable to load map. Please try again later.</p>
                <div class="map-address">${customer?.address || 'No address available'}</div>
              </div>
            `;
          }
        }
      }
    }
    
    // Toggle edit mode for financial information
    function toggleEditFinancial() {
      if (!editingFinancial && customer) {
        editFinancialData = {
          creditLimit: customer.creditLimit || 0,
          preferredPaymentMethod: customer.preferredPaymentMethod || ''
        };
      }
      editingFinancial = !editingFinancial;
    }
    
    // Save financial information
    function saveFinancialInfo() {
      if (customer) {
        customer.creditLimit = editFinancialData.creditLimit;
        customer.preferredPaymentMethod = editFinancialData.preferredPaymentMethod;
        // In a real app, this would be an API call to update the data
      }
      editingFinancial = false;
    }
    
    // Generate invoice
    function generateInvoice() {
      alert(`Generating new invoice for ${customer?.name}`);
      // In a real app, this would open a form to create a new invoice
    }
    
    // Toggle document upload form
    function toggleDocumentUpload() {
      uploadingDocument = !uploadingDocument;
      if (!uploadingDocument) {
        newDocument = {
          name: '',
          type: 'contract',
          file: null
        };
      }
    }
    
    // Handle file selection
    function handleFileSelect(event: Event) {
      const input = event.target as HTMLInputElement;
      if (input.files && input.files.length > 0) {
        newDocument.file = input.files[0];
        if (!newDocument.name) {
          newDocument.name = newDocument.file.name.split('.')[0];
        }
      }
    }
    
    // Upload document
    function uploadDocument() {
      if (newDocument.name && newDocument.file) {
        // In a real app, this would upload the file to a server
        const newDoc = {
          id: `DOC-${Math.floor(Math.random() * 1000)}`,
          name: newDocument.name,
          type: newDocument.type,
          date: new Date().toISOString().split('T')[0],
          size: `${(newDocument.file.size / (1024 * 1024)).toFixed(1)} MB`
        };
        
        // Add to documents array
        documents.push(newDoc);
        documents = [...documents]; // Trigger reactivity
        
        // Reset form
        toggleDocumentUpload();
        alert('Document uploaded successfully!');
      } else {
        alert('Please provide a name and select a file.');
      }
    }
    
    // Document functions
    function downloadDocument(doc: {id: string, name: string, type: string, date: string, size: string}) {
      alert(`Downloading document: ${doc.name}. In a real app, this would initiate the download process.`);
    }
    
    function viewDocument(doc: {id: string, name: string, type: string, date: string, size: string}) {
      alert(`Opening document viewer for: ${doc.name}\n\nIn a production environment, this would open a document preview.`);
    }
    
    // Delete document
    function deleteDocument(docId: string) {
      if (confirm('Are you sure you want to delete this document?')) {
        documents = documents.filter(d => d.id !== docId);
        alert('Document deleted successfully!');
      }
    }
    
    // Toggle add address form
    function toggleAddAddress() {
      addingAddress = !addingAddress;
      if (!addingAddress) {
        newAddressData = {
          label: '',
          address: ''
        };
      }
    }
    
    // Add new address
    function addNewAddress() {
      if (newAddressData.label && newAddressData.address) {
        const newAddress = {
          id: `ADDR-${Math.floor(Math.random() * 1000)}`,
          label: newAddressData.label,
          address: newAddressData.address,
          isPrimary: false
        };
        
        alternativeAddresses = [...alternativeAddresses, newAddress];
        toggleAddAddress();
        
        // Re-initialize map to show new address
        mapLoaded = false;
        setTimeout(initMap, 100);
      } else {
        alert('Please provide both a label and address.');
      }
    }
    
    // Set primary address
    function setPrimaryAddress(addressId: string) {
      alternativeAddresses = alternativeAddresses.map(addr => ({
        ...addr,
        isPrimary: addr.id === addressId
      }));
      
      // Re-initialize map for the new primary address
      mapLoaded = false;
      setTimeout(initMap, 100);
    }
    
    // View address on map
    function viewAddressOnMap(address: string) {
      alert(`Showing address on map: ${address}`);
      // In a real implementation, this would center the map on this address
    }
    
    // Initialize map when the tab changes
    $: if (activeTab === 'delivery') {
      setTimeout(initMap, 100);
    }
  </script>

<svelte:head>
  <title>{customer ? `${customer.name} | Customer Details` : 'Customer Details'} | BroilerPro</title>
</svelte:head>

<div class="customer-details-page">
  {#if loading}
    <div class="loading-state">
      <div class="spinner"></div>
      <p>Loading customer details...</p>
    </div>
  {:else if error}
    <div class="error-state">
      <span class="material-icons">error_outline</span>
      <h2>Error Loading Customer</h2>
      <p>There was a problem loading the customer details. Please try again.</p>
      <button class="btn btn-primary" on:click={() => window.location.reload()}>Retry</button>
      <button class="btn btn-outline-secondary" on:click={() => goto('/customers')}>
        Back to Customers
      </button>
    </div>
  {:else if customer}
    <!-- Page Header -->
    <div class="page-header">
      <div>
        <div class="breadcrumbs">
          <a href="/customers">Customers</a>
          <span class="material-icons">chevron_right</span>
          <span>{customer.name}</span>
        </div>
        <div class="customer-header">
          <h1 class="page-title">{customer.name}</h1>
          <span class="badge badge-{customer.status}">{customer.status}</span>
        </div>
        <p class="customer-type">{customer.type} • {customer.id}</p>
      </div>
      
      <div class="page-actions">
        <button class="btn btn-outline-secondary">
          <span class="material-icons">email</span>
          Contact
        </button>
        <button class="btn btn-outline-primary">
          <span class="material-icons">receipt</span>
          Generate Invoice
        </button>
        <button class="btn btn-primary" on:click={() => customer && goto(`/customers/edit/${customer.id}`)}>
          <span class="material-icons">edit</span>
          Edit
        </button>
      </div>
    </div>
    
    <!-- Quick Stats -->
    <div class="stats-summary">
      <div class="stat-card">
        <div class="stat-icon">
          <span class="material-icons">shopping_cart</span>
        </div>
        <div class="stat-content">
          <p class="stat-value">{customer.totalOrders}</p>
          <p class="stat-label">Total Orders</p>
        </div>
      </div>
      
      <div class="stat-card">
        <div class="stat-icon">
          <span class="material-icons">payments</span>
        </div>
        <div class="stat-content">
          <p class="stat-value">{formatCurrency(customer.totalSpend)}</p>
          <p class="stat-label">Total Spend</p>
        </div>
      </div>
      
      <div class="stat-card">
        <div class="stat-icon">
          <span class="material-icons">event</span>
        </div>
        <div class="stat-content">
          <p class="stat-value">{formatDate(customer.lastOrder)}</p>
          <p class="stat-label">Last Order</p>
        </div>
      </div>
      
      <div class="stat-card">
        <div class="stat-icon">
          <span class="material-icons">account_balance_wallet</span>
        </div>
        <div class="stat-content">
          <p class="stat-value">{formatCurrency(customer.creditLimit || 0)}</p>
          <p class="stat-label">Credit Limit</p>
        </div>
      </div>
    </div>
    
    <!-- Tab Navigation -->
    <div class="tabs-container">
      <div class="tabs">
        <button 
          class="tab {activeTab === 'overview' ? 'active' : ''}" 
          on:click={() => setActiveTab('overview')}
        >
          <span class="material-icons">dashboard</span>
          Overview
        </button>
        <button 
          class="tab {activeTab === 'orders' ? 'active' : ''}" 
          on:click={() => setActiveTab('orders')}
        >
          <span class="material-icons">shopping_cart</span>
          Orders
        </button>
        <button 
          class="tab {activeTab === 'financial' ? 'active' : ''}" 
          on:click={() => setActiveTab('financial')}
        >
          <span class="material-icons">payments</span>
          Financial
        </button>
        <button 
          class="tab {activeTab === 'delivery' ? 'active' : ''}" 
          on:click={() => setActiveTab('delivery')}
        >
          <span class="material-icons">local_shipping</span>
          Delivery
        </button>
        <button 
          class="tab {activeTab === 'documents' ? 'active' : ''}" 
          on:click={() => setActiveTab('documents')}
        >
          <span class="material-icons">description</span>
          Documents
        </button>
        <button 
          class="tab {activeTab === 'notes' ? 'active' : ''}" 
          on:click={() => setActiveTab('notes')}
        >
          <span class="material-icons">event_note</span>
          Notes & Activity
        </button>
      </div>
    </div>
    
    <!-- Tab Content -->
    <div class="tab-content">
      <!-- Overview Tab -->
      <div class="tab-pane" class:active={activeTab === 'overview'}>
          <div class="section-grid">
            <!-- Contact Information -->
            <div class="card">
              <div class="card-header">
                <h2>Contact Information</h2>
                <button class="btn-icon" on:click={toggleEditContact}>
                  <span class="material-icons">{editingContact ? 'close' : 'edit'}</span>
                </button>
              </div>
              <div class="card-body">
                {#if !editingContact}
                  <div class="info-row">
                    <div class="info-label">Contact Person</div>
                    <div class="info-value">{customer.contactPerson}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Email</div>
                    <div class="info-value">
                      <a href="mailto:{customer.email}">{customer.email}</a>
                    </div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Phone</div>
                    <div class="info-value">
                      <a href="tel:{customer.phone}">{customer.phone}</a>
                    </div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Address</div>
                    <div class="info-value">{customer.address}</div>
                  </div>
                {:else}
                  <div class="form-grid">
                    <div class="form-group">
                      <label for="contactPerson">Contact Person</label>
                      <input 
                        type="text" 
                        id="contactPerson" 
                        class="form-control" 
                        bind:value={editContactData.contactPerson}
                      />
                    </div>
                    <div class="form-group">
                      <label for="email">Email</label>
                      <input 
                        type="email" 
                        id="email" 
                        class="form-control" 
                        bind:value={editContactData.email}
                      />
                    </div>
                    <div class="form-group">
                      <label for="phone">Phone</label>
                      <input 
                        type="tel" 
                        id="phone" 
                        class="form-control" 
                        bind:value={editContactData.phone}
                      />
                    </div>
                    <div class="form-group wide">
                      <label for="address">Address</label>
                      <input 
                        type="text" 
                        id="address" 
                        class="form-control" 
                        bind:value={editContactData.address}
                      />
                    </div>
                    <div class="form-actions">
                      <button class="btn btn-secondary" on:click={toggleEditContact}>Cancel</button>
                      <button class="btn btn-primary" on:click={saveContactInfo}>Save Changes</button>
                    </div>
                  </div>
                {/if}
              </div>
            </div>
            
            <!-- Business Information -->
            <div class="card">
              <div class="card-header">
                <h2>Business Information</h2>
                <button class="btn-icon" on:click={toggleEditBusiness}>
                  <span class="material-icons">{editingBusiness ? 'close' : 'edit'}</span>
                </button>
              </div>
              <div class="card-body">
                {#if !editingBusiness}
                  <div class="info-row">
                    <div class="info-label">VAT Number</div>
                    <div class="info-value">{customer.vatNumber || 'Not provided'}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Registration Number</div>
                    <div class="info-value">{customer.registrationNumber || 'Not provided'}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Customer Since</div>
                    <div class="info-value">{customer.createdAt ? formatDate(customer.createdAt) : 'Not available'}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Payment Terms</div>
                    <div class="info-value">{customer.paymentTerms || 'Not specified'}</div>
                  </div>
                {:else}
                  <div class="form-grid">
                    <div class="form-group">
                      <label for="vatNumber">VAT Number</label>
                      <input 
                        type="text" 
                        id="vatNumber" 
                        class="form-control" 
                        bind:value={editBusinessData.vatNumber}
                      />
                    </div>
                    <div class="form-group">
                      <label for="registrationNumber">Registration Number</label>
                      <input 
                        type="text" 
                        id="registrationNumber" 
                        class="form-control" 
                        bind:value={editBusinessData.registrationNumber}
                      />
                    </div>
                    <div class="form-group">
                      <label for="paymentTerms">Payment Terms</label>
                      <select 
                        id="paymentTerms" 
                        class="form-control" 
                        bind:value={editBusinessData.paymentTerms}
                      >
                        <option value="">Select payment terms</option>
                        <option value="Net 15">Net 15</option>
                        <option value="Net 30">Net 30</option>
                        <option value="Net 45">Net 45</option>
                        <option value="Net 60">Net 60</option>
                        <option value="Due on Receipt">Due on Receipt</option>
                      </select>
                    </div>
                    <div class="form-actions">
                      <button class="btn btn-secondary" on:click={toggleEditBusiness}>Cancel</button>
                      <button class="btn btn-primary" on:click={saveBusinessInfo}>Save Changes</button>
                    </div>
                  </div>
                {/if}
              </div>
            </div>
          </div>
          
          <!-- Recent Orders -->
          <div class="card">
            <div class="card-header">
              <h2>Recent Orders</h2>
              <button class="btn-text" on:click={() => setActiveTab('orders')}>
                View All
                <span class="material-icons">chevron_right</span>
              </button>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table class="table">
                  <thead>
                    <tr>
                      <th>Order ID</th>
                      <th>Date</th>
                      <th>Amount</th>
                      <th>Status</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each recentOrders.filter(order => orderStatusFilter === 'all' || order.status === orderStatusFilter) as order}
                      <tr>
                        <td>{order.id}</td>
                        <td>{formatDate(order.date)}</td>
                        <td>{formatCurrency(order.amount)}</td>
                        <td>
                          <span class="status-badge {order.status}">
                            {order.status}
                          </span>
                        </td>
                        <td>
                          <div class="action-buttons">
                            <button class="btn-icon" title="View Order">
                              <span class="material-icons">visibility</span>
                            </button>
                            <button class="btn-icon" title="Download Invoice">
                              <span class="material-icons">download</span>
                            </button>
                          </div>
                        </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
          
          <!-- Documents -->
          <div class="card">
            <div class="card-header">
              <h2>Documents</h2>
              <div class="header-actions">
                <div class="filter-dropdown">
                  <select class="form-select" bind:value={documentTypeFilter}>
                    <option value="all">All Types</option>
                    <option value="contract">Contracts</option>
                    <option value="certificate">Certificates</option>
                    <option value="legal">Legal</option>
                    <option value="financial">Financial</option>
                    <option value="other">Other</option>
                  </select>
                </div>
                <button class="btn btn-primary" on:click={toggleDocumentUpload}>
                  <span class="material-icons">upload</span>
                  Upload Document
                </button>
              </div>
            </div>
            <div class="card-body">
              {#if uploadingDocument}
                <div class="upload-form">
                  <h3 class="form-heading">Upload New Document</h3>
                  <div class="form-grid">
                    <div class="form-group">
                      <label for="docName">Document Name</label>
                      <input 
                        type="text" 
                        id="docName" 
                        class="form-control" 
                        bind:value={newDocument.name}
                        placeholder="Enter document name"
                      />
                    </div>
                    <div class="form-group">
                      <label for="docType">Document Type</label>
                      <select 
                        id="docType" 
                        class="form-control" 
                        bind:value={newDocument.type}
                      >
                        <option value="contract">Contract</option>
                        <option value="certificate">Certificate</option>
                        <option value="legal">Legal Document</option>
                        <option value="financial">Financial Document</option>
                        <option value="other">Other</option>
                      </select>
                    </div>
                    <div class="form-group wide">
                      <label for="docFile">Select File</label>
                      <input 
                        type="file" 
                        id="docFile" 
                        class="form-control" 
                        on:change={handleFileSelect}
                      />
                    </div>
                    <div class="form-actions">
                      <button class="btn btn-secondary" on:click={toggleDocumentUpload}>
                        Cancel
                      </button>
                      <button class="btn btn-primary" on:click={uploadDocument}>
                        Upload Document
                      </button>
                    </div>
                  </div>
                </div>
              {:else}
                <div class="table-responsive">
                  <table class="table">
                    <thead>
                      <tr>
                        <th>Name</th>
                        <th>Type</th>
                        <th>Date</th>
                        <th>Size</th>
                        <th>Actions</th>
                      </tr>
                    </thead>
                    <tbody>
                      {#each documents.filter(doc => documentTypeFilter === 'all' || doc.type === documentTypeFilter) as doc}
                        <tr>
                          <td>{doc.name}</td>
                          <td>{doc.type.charAt(0).toUpperCase() + doc.type.slice(1)}</td>
                          <td>{formatDate(doc.date)}</td>
                          <td>{doc.size}</td>
                          <td>
                            <div class="table-actions">
                              <button class="btn-icon" title="View Document" on:click={() => viewDocument(doc)}>
                                <span class="material-icons">visibility</span>
                              </button>
                              <button class="btn-icon" title="Download Document" on:click={() => downloadDocument(doc)}>
                                <span class="material-icons">download</span>
                              </button>
                              <button class="btn-icon" title="Delete Document" on:click={() => deleteDocument(doc.id)}>
                                <span class="material-icons">delete</span>
                              </button>
                            </div>
                          </td>
                        </tr>
                      {/each}
                    </tbody>
                  </table>
                </div>
              {/if}
            </div>
          </div>
          
          <!-- Notes -->
          <div class="card">
              <div class="card-header">
                <h2>Notes</h2>
                <button class="btn-icon" on:click={toggleEditNotes}>
                  <span class="material-icons">{editingNotes ? 'close' : 'edit'}</span>
                </button>
              </div>
              <div class="card-body">
                {#if !editingNotes}
                  <p class="notes-content">{customer.notes || 'No notes available for this customer.'}</p>
                {:else}
                  <div class="form-group wide">
                    <label for="notes">Customer Notes</label>
                    <textarea 
                      id="notes" 
                      class="form-control" 
                      rows="5" 
                      bind:value={editNotesData.notes}
                    ></textarea>
                    <div class="form-actions">
                      <button class="btn btn-secondary" on:click={toggleEditNotes}>Cancel</button>
                      <button class="btn btn-primary" on:click={saveNotes}>Save Notes</button>
                    </div>
                  </div>
                {/if}
              </div>
          </div>
        </div>
      
      <!-- Orders Tab -->
      <div class="tab-pane" class:active={activeTab === 'orders'}>
          <div class="card">
            <div class="card-header">
              <h2>Order History</h2>
              <div class="header-actions">
                <div class="filter-dropdown">
                  <select class="form-select" bind:value={orderStatusFilter}>
                    <option value="all">All Statuses</option>
                    <option value="processing">Processing</option>
                    <option value="shipped">Shipped</option>
                    <option value="delivered">Delivered</option>
                    <option value="cancelled">Cancelled</option>
                  </select>
                </div>
                <div class="filter-dropdown">
                  <select class="form-select" bind:value={orderDateFilter}>
                    <option value="all">All Time</option>
                    <option value="month">Last Month</option>
                    <option value="quarter">Last Quarter</option>
                    <option value="year">Last Year</option>
                  </select>
                </div>
              </div>
            </div>
            <div class="card-body">
              <div class="search-box">
                <span class="material-icons search-icon">search</span>
                <input type="text" placeholder="Search orders..." />
              </div>
              <div class="table-responsive">
                <table class="table">
                  <thead>
                    <tr>
                      <th>Order ID</th>
                      <th>Date</th>
                      <th>Products</th>
                      <th>Amount</th>
                      <th>Status</th>
                      <th>Payment</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each recentOrders.filter(order => orderStatusFilter === 'all' || order.status === orderStatusFilter) as order}
                      <tr>
                        <td>{order.id}</td>
                        <td>{formatDate(order.date)}</td>
                        <td>Various Products</td>
                        <td>{formatCurrency(order.amount)}</td>
                        <td>
                          <span class="status-badge {order.status}">
                            {order.status}
                          </span>
                        </td>
                        <td>
                          <span class="status-badge completed">
                            Paid
                          </span>
                        </td>
                        <td>
                          <div class="action-buttons">
                            <button class="btn-icon" title="View Order" on:click={() => viewOrder(order.id)}>
                              <span class="material-icons">visibility</span>
                            </button>
                            <button class="btn-icon" title="Download Invoice" on:click={() => downloadInvoice(order.id)}>
                              <span class="material-icons">download</span>
                            </button>
                            <button class="btn-icon" title="More Options" on:click={() => alert(`More options for order ${order.id}`)}>
                              <span class="material-icons">more_vert</span>
                            </button>
                          </div>
                        </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      
      <!-- Financial Tab -->
      <div class="tab-pane" class:active={activeTab === 'financial'}>
          <div class="section-grid">
            <!-- Payment Information -->
            <div class="card">
              <div class="card-header">
                <h2>Payment Information</h2>
                <button class="btn-icon" on:click={toggleEditFinancial}>
                  <span class="material-icons">{editingFinancial ? 'close' : 'edit'}</span>
                </button>
              </div>
              <div class="card-body">
                {#if !editingFinancial}
                  <div class="info-row">
                    <div class="info-label">Credit Limit</div>
                    <div class="info-value">{formatCurrency(customer.creditLimit || 0)}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Payment Terms</div>
                    <div class="info-value">{customer.paymentTerms || 'Not specified'}</div>
                  </div>
                  <div class="info-row">
                    <div class="info-label">Preferred Payment Method</div>
                    <div class="info-value">{customer.preferredPaymentMethod || 'Not specified'}</div>
                  </div>
                {:else}
                  <div class="form-grid">
                    <div class="form-group">
                      <label for="creditLimit">Credit Limit</label>
                      <input 
                        type="number" 
                        id="creditLimit" 
                        class="form-control" 
                        bind:value={editFinancialData.creditLimit}
                      />
                    </div>
                    <div class="form-group">
                      <label for="preferredPaymentMethod">Preferred Payment Method</label>
                      <select 
                        id="preferredPaymentMethod" 
                        class="form-control" 
                        bind:value={editFinancialData.preferredPaymentMethod}
                      >
                        <option value="">Select payment method</option>
                        <option value="Bank Transfer">Bank Transfer</option>
                        <option value="Credit Card">Credit Card</option>
                        <option value="Check">Check</option>
                        <option value="Cash">Cash</option>
                        <option value="Digital Payment">Digital Payment</option>
                      </select>
                    </div>
                    <div class="form-actions">
                      <button class="btn btn-secondary" on:click={toggleEditFinancial}>Cancel</button>
                      <button class="btn btn-primary" on:click={saveFinancialInfo}>Save Changes</button>
                    </div>
                  </div>
                {/if}
                <div class="info-row">
                  <div class="info-label">Account Status</div>
                  <div class="info-value">
                    <span class="badge {customer?.status === 'active' ? 'badge-active' : 'badge-inactive'}">{customer?.status ? (customer.status.charAt(0).toUpperCase() + customer.status.slice(1)) : 'Unknown'}</span>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Financial Summary -->
            <div class="card">
              <div class="card-header">
                <h2>Financial Summary</h2>
              </div>
              <div class="card-body">
                <div class="financial-summary">
                  <div class="summary-item">
                    <div class="summary-label">Total Spend</div>
                    <div class="summary-value">{formatCurrency(customer.totalSpend)}</div>
                  </div>
                  <div class="summary-item">
                    <div class="summary-label">Outstanding Balance</div>
                    <div class="summary-value">{formatCurrency(8750)}</div>
                  </div>
                  <div class="summary-item">
                    <div class="summary-label">Available Credit</div>
                    <div class="summary-value">{formatCurrency((customer.creditLimit || 0) - 8750)}</div>
                  </div>
                  <div class="summary-item">
                    <div class="summary-label">Average Order Value</div>
                    <div class="summary-value">{formatCurrency(customer.totalSpend / customer.totalOrders)}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Payment History -->
          <div class="card">
            <div class="card-header">
              <h2>Payment History</h2>
              <button class="btn btn-outline-primary" on:click={generateInvoice}>
                <span class="material-icons">receipt</span>
                Generate Invoice
              </button>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table class="table">
                  <thead>
                    <tr>
                      <th>Payment ID</th>
                      <th>Date</th>
                      <th>Amount</th>
                      <th>Method</th>
                      <th>Status</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each paymentHistory as payment}
                      <tr>
                        <td>{payment.id}</td>
                        <td>{formatDate(payment.date)}</td>
                        <td>{formatCurrency(payment.amount)}</td>
                        <td>
                          <span class="payment-method">
                            <span class="material-icons">
                              {payment.method === 'bank_transfer' ? 'account_balance' : 'credit_card'}
                            </span>
                            {payment.method === 'bank_transfer' ? 'Bank Transfer' : 'Credit Card'}
                          </span>
                        </td>
                        <td>
                          <span class="status-badge completed">
                            {payment.status}
                          </span>
                        </td>
                        <td>
                          <div class="action-buttons">
                            <button class="btn-icon" title="View Receipt" on:click={() => alert(`Viewing receipt for payment ${payment.id}`)}>
                              <span class="material-icons">visibility</span>
                            </button>
                            <button class="btn-icon" title="Download Receipt" on:click={() => alert(`Downloading receipt for payment ${payment.id}`)}>
                              <span class="material-icons">download</span>
                            </button>
                          </div>
                        </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      
      <!-- Delivery Tab -->
      <div class="tab-pane" class:active={activeTab === 'delivery'}>
          <div class="section-grid">
            <!-- Primary Address -->
            <div class="card">
              <div class="card-header">
                <h2>Primary Address</h2>
              </div>
              <div class="card-body">
                <div class="address-card primary">
                  <div class="address-badge">Primary</div>
                  <p class="address-line">{customer.name}</p>
                  <p class="address-line">Attn: {customer.contactPerson}</p>
                  <p class="address-line">{customer.address}</p>
                  <div class="address-actions">
                    <button class="btn-text" on:click={() => viewAddressOnMap(customer.address)}>
                      <span class="material-icons">map</span>
                      View on Map
                    </button>
                  </div>
                </div>
                
                {#if alternativeAddresses.length > 0}
                  <h3 class="section-subheading">Alternative Addresses</h3>
                  {#each alternativeAddresses as address}
                    <div class="address-card {address.isPrimary ? 'primary' : ''}">
                      <div class="address-badge">{address.label}</div>
                      <p class="address-line">{customer.name}</p>
                      <p class="address-line">Attn: {customer.contactPerson}</p>
                      <p class="address-line">{address.address}</p>
                      <div class="address-actions">
                        <button class="btn-text" on:click={() => viewAddressOnMap(address.address)}>
                          <span class="material-icons">map</span>
                          View on Map
                        </button>
                        {#if !address.isPrimary}
                          <button class="btn-text" on:click={() => setPrimaryAddress(address.id)}>
                            <span class="material-icons">star_outline</span>
                            Set as Primary
                          </button>
                        {/if}
                      </div>
                    </div>
                  {/each}
                {/if}
                
                <div id="map" class="map-container"></div>
                <div class="map-address-text">
                  {#if customer !== null}
                    <span class="material-icons">location_on</span>
                    {customer.address}
                  {/if}
                </div>
                
                {#if addingAddress}
                  <div class="new-address-form">
                    <h3 class="form-heading">Add New Address</h3>
                    <div class="form-grid">
                      <div class="form-group">
                        <label for="addressLabel">Address Label</label>
                        <input 
                          type="text" 
                          id="addressLabel" 
                          class="form-control" 
                          bind:value={newAddressData.label}
                          placeholder="E.g., Warehouse, Office, etc."
                        />
                      </div>
                      <div class="form-group wide">
                        <label for="newAddress">Address</label>
                        <input 
                          type="text" 
                          id="newAddress" 
                          class="form-control" 
                          bind:value={newAddressData.address}
                          placeholder="Enter full address"
                        />
                      </div>
                      <div class="form-actions">
                        <button class="btn btn-secondary" on:click={toggleAddAddress}>
                          Cancel
                        </button>
                        <button class="btn btn-primary" on:click={addNewAddress}>
                          Add Address
                        </button>
                      </div>
                    </div>
                  </div>
                {:else}
                  <button class="btn btn-outline-secondary mt-3" on:click={toggleAddAddress}>
                    <span class="material-icons">add</span>
                    Add Alternative Address
                  </button>
                {/if}
              </div>
            </div>
            
            <!-- Delivery Preferences -->
            <div class="card">
              <div class="card-header">
                <h2>Delivery Preferences</h2>
                <button class="btn-icon" on:click={() => alert('Edit delivery preferences functionality will be implemented here')}>
                  <span class="material-icons">edit</span>
                </button>
              </div>
              <div class="card-body">
                <div class="info-row">
                  <div class="info-label">Preferred Days</div>
                  <div class="info-value">Monday - Friday</div>
                </div>
                <div class="info-row">
                  <div class="info-label">Preferred Time</div>
                  <div class="info-value">9:00 AM - 12:00 PM</div>
                </div>
                <div class="info-row">
                  <div class="info-label">Special Instructions</div>
                  <div class="info-value">Please call 30 minutes before delivery. Delivery entrance is at the back of the building.</div>
                </div>
                <div class="info-row">
                  <div class="info-label">Delivery Contact</div>
                  <div class="info-value">{customer.contactPerson}, {customer.phone}</div>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Delivery History -->
          <div class="card">
            <div class="card-header">
              <h2>Delivery History</h2>
              <div class="header-actions">
                <div class="filter-dropdown">
                  <select class="form-select" bind:value={deliveryStatusFilter}>
                    <option value="all">All Statuses</option>
                    <option value="scheduled">Scheduled</option>
                    <option value="in_transit">In Transit</option>
                    <option value="delivered">Delivered</option>
                    <option value="failed">Failed</option>
                  </select>
                </div>
              </div>
            </div>
            <div class="card-body">
              <div class="table-responsive">
                <table class="table">
                  <thead>
                    <tr>
                      <th>Order ID</th>
                      <th>Delivery Date</th>
                      <th>Status</th>
                      <th>Carrier</th>
                      <th>Tracking</th>
                      <th>Received By</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each deliveryHistory.filter(delivery => deliveryStatusFilter === 'all' || delivery.status === deliveryStatusFilter) as delivery}
                      <tr>
                        <td>{delivery.id}</td>
                        <td>{formatDate(delivery.date)}</td>
                        <td>
                          <span class="status-badge {delivery.status}">
                            {delivery.status}
                          </span>
                        </td>
                        <td>BroilerPro Logistics</td>
                        <td>
                          {#if delivery.status === 'delivered'}
                            TRK-{100000 + parseInt(delivery.id.replace('DEL-2023-00', ''))}
                          {:else if delivery.status === 'processing'}
                            Pending
                          {:else}
                            N/A
                          {/if}
                        </td>
                        <td>
                          {#if delivery.status === 'delivered'}
                            {customer.contactPerson}
                          {:else}
                            -
                          {/if}
                        </td>
                        <td>
                          <div class="action-buttons">
                            <button class="btn-icon" title="View Details">
                              <span class="material-icons">visibility</span>
                            </button>
                            {#if delivery.status === 'delivered'}
                              <button class="btn-icon" title="Proof of Delivery">
                                <span class="material-icons">description</span>
                              </button>
                            {/if}
                          </div>
                        </td>
                      </tr>
                    {/each}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      
      <!-- Documents Tab -->
      <div class="tab-pane" class:active={activeTab === 'documents'}>
          <div class="card">
            <div class="card-header">
              <h2>Documents</h2>
              <button class="btn btn-primary">
                <span class="material-icons">upload_file</span>
                Upload Document
              </button>
            </div>
            <div class="card-body">
              <div class="document-filters">
                <div class="search-box">
                  <span class="material-icons search-icon">search</span>
                  <input type="text" placeholder="Search documents..." />
                </div>
                <div class="filter-group">
                  <label for="doc-type-filter">Type:</label>
                  <select id="doc-type-filter" class="form-select">
                    <option value="all">All Types</option>
                    <option value="contract">Contracts</option>
                    <option value="invoice">Invoices</option>
                    <option value="certificate">Certificates</option>
                    <option value="legal">Legal</option>
                  </select>
                </div>
              </div>
              
              <div class="documents-grid">
                {#each documents as doc}
                  <div class="document-card">
                    <div class="document-icon">
                      <span class="material-icons">
                        {doc.type === 'contract' ? 'description' : 
                         doc.type === 'certificate' ? 'verified' : 
                         doc.type === 'legal' ? 'gavel' : 'insert_drive_file'}
                      </span>
                    </div>
                    <div class="document-info">
                      <h3 class="document-name">{doc.name}</h3>
                      <p class="document-meta">
                        {doc.type.charAt(0).toUpperCase() + doc.type.slice(1)} • {formatDate(doc.date)} • {doc.size}
                      </p>
                    </div>
                    <div class="document-actions">
                      <button class="btn-icon" title="View Document">
                        <span class="material-icons">visibility</span>
                      </button>
                      <button class="btn-icon" title="Download">
                        <span class="material-icons">download</span>
                      </button>
                    </div>
                  </div>
                {/each}
              </div>
            </div>
          </div>
        </div>
      
      <!-- Notes Tab -->
      <div class="tab-pane" class:active={activeTab === 'notes'}>
          <div class="section-grid">
            <!-- Customer Notes -->
            <div class="card">
              <div class="card-header">
                <h2>Customer Notes</h2>
                <button class="btn-icon">
                  <span class="material-icons">edit</span>
                </button>
              </div>
              <div class="card-body">
                <div class="notes-editor">
                  <textarea 
                    class="form-control" 
                    rows="6" 
                    placeholder="Add notes about this customer..."
                  >{customer.notes || ''}</textarea>
                  <div class="notes-actions">
                    <button class="btn btn-primary">
                      <span class="material-icons">save</span>
                      Save Notes
                    </button>
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Communication Log -->
            <div class="card">
              <div class="card-header">
                <h2>Communication Log</h2>
                <button class="btn btn-outline-primary">
                  <span class="material-icons">add</span>
                  Add Communication
                </button>
              </div>
              <div class="card-body">
                <div class="communication-log">
                  <div class="comm-item">
                    <div class="comm-icon email">
                      <span class="material-icons">email</span>
                    </div>
                    <div class="comm-content">
                      <div class="comm-header">
                        <h3>Email: Order Confirmation</h3>
                        <span class="comm-date">Today, 11:45 AM</span>
                      </div>
                      <p>Sent order confirmation for #ORD-2023-001</p>
                      <div class="comm-meta">
                        <span class="comm-recipient">To: {customer.email}</span>
                        <span class="comm-sender">By: Sarah Johnson</span>
                      </div>
                      <div class="comm-actions">
                        <button class="btn-text">View Email</button>
                      </div>
                    </div>
                  </div>
                  
                  <div class="comm-item">
                    <div class="comm-icon phone">
                      <span class="material-icons">phone</span>
                    </div>
                    <div class="comm-content">
                      <div class="comm-header">
                        <h3>Phone Call: Delivery Schedule</h3>
                        <span class="comm-date">Yesterday, 3:30 PM</span>
                      </div>
                      <p>Discussed delivery schedule for upcoming order</p>
                      <div class="comm-meta">
                        <span class="comm-recipient">With: {customer.contactPerson}</span>
                        <span class="comm-sender">By: Michael Davis</span>
                      </div>
                      <div class="comm-actions">
                        <button class="btn-text">View Call Notes</button>
                      </div>
                    </div>
                  </div>
                  
                  <div class="comm-item">
                    <div class="comm-icon meeting">
                      <span class="material-icons">groups</span>
                    </div>
                    <div class="comm-content">
                      <div class="comm-header">
                        <h3>Meeting: Quarterly Review</h3>
                        <span class="comm-date">Oct 18, 2023</span>
                      </div>
                      <p>Quarterly business review meeting with customer</p>
                      <div class="comm-meta">
                        <span class="comm-recipient">With: {customer.contactPerson} and Team</span>
                        <span class="comm-sender">By: Sales Team</span>
                      </div>
                      <div class="comm-actions">
                        <button class="btn-text">View Meeting Notes</button>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Activity Timeline -->
          <div class="card">
            <div class="card-header">
              <h2>Activity Timeline</h2>
              <div class="header-actions">
                <div class="filter-dropdown">
                  <select class="form-select" bind:value={activityFilter}>
                    <option value="all">All Activities</option>
                    <option value="orders">Orders</option>
                    <option value="payments">Payments</option>
                    <option value="deliveries">Deliveries</option>
                    <option value="communication">Communication</option>
                  </select>
                </div>
              </div>
            </div>
            <div class="card-body">
              <div class="timeline">
                {#each activityFilter === 'all' || activityFilter === 'orders' ? [1] : [] as _}
                  <div class="timeline-item">
                    <div class="timeline-icon">
                      <span class="material-icons">shopping_cart</span>
                    </div>
                    <div class="timeline-content">
                      <div class="timeline-header">
                        <h3>Order Placed</h3>
                        <span class="timeline-date">Nov 2, 2023</span>
                      </div>
                      <p>Customer placed order #ORD-2023-001 for 450 broiler chickens</p>
                      <div class="timeline-meta">Amount: {formatCurrency(12500)}</div>
                    </div>
                  </div>
                {/each}
                
                {#each activityFilter === 'all' || activityFilter === 'payments' ? [1] : [] as _}
                  <div class="timeline-item">
                    <div class="timeline-icon">
                      <span class="material-icons">payments</span>
                    </div>
                    <div class="timeline-content">
                      <div class="timeline-header">
                        <h3>Payment Received</h3>
                        <span class="timeline-date">Nov 5, 2023</span>
                      </div>
                      <p>Received payment for invoice #INV-2023-001</p>
                      <div class="timeline-meta">Amount: {formatCurrency(12500)}</div>
                    </div>
                  </div>
                {/each}
                
                {#each activityFilter === 'all' || activityFilter === 'deliveries' ? [1] : [] as _}
                  <div class="timeline-item">
                    <div class="timeline-icon">
                      <span class="material-icons">local_shipping</span>
                    </div>
                    <div class="timeline-content">
                      <div class="timeline-header">
                        <h3>Delivery Completed</h3>
                        <span class="timeline-date">Nov 3, 2023</span>
                      </div>
                      <p>Order #ORD-2023-001 was delivered successfully</p>
                      <div class="timeline-meta">Driver: Michael Johnson</div>
                    </div>
                  </div>
                {/each}
                
                {#each activityFilter === 'all' || activityFilter === 'communication' ? [1] : [] as _}
                  <div class="timeline-item">
                    <div class="timeline-icon">
                      <span class="material-icons">meeting_room</span>
                    </div>
                    <div class="timeline-content">
                      <div class="timeline-header">
                        <h3>Meeting</h3>
                        <span class="timeline-date">Sep 30, 2023</span>
                      </div>
                      <p>Quarterly business review meeting with customer</p>
                      <div class="timeline-meta">Attendees: John Smith, Sales Team</div>
                    </div>
                  </div>
                {/each}
                
                <div class="timeline-item">
                  <div class="timeline-icon">
                    <span class="material-icons">account_balance_wallet</span>
                  </div>
                  <div class="timeline-content">
                    <div class="timeline-header">
                      <h3>Credit Limit Updated</h3>
                      <span class="timeline-date">Oct 5, 2023</span>
                    </div>
                    <p>Credit limit increased from ZAR 200,000 to ZAR 250,000</p>
                    <div class="timeline-actions">
                      <button class="btn-text">View Details</button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
    </div>
  {/if}
</div>

<style>
  .customer-details-page {
    padding: 24px;
    max-width: 95%;
    margin: 0 auto;
  }
  
  .loading-state,
  .error-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 48px 0;
    text-align: center;
  }
  
  .spinner {
    width: 40px;
    height: 40px;
    border: 4px solid rgba(0, 0, 0, 0.1);
    border-left-color: var(--primary);
    border-radius: 50%;
    animation: spin 1s linear infinite;
    margin-bottom: 16px;
  }
  
  @keyframes spin {
    to { transform: rotate(360deg); }
  }
  
  .error-state .material-icons {
    font-size: 48px;
    color: #f44336;
    margin-bottom: 16px;
  }
  
  .error-state h2 {
    margin: 0 0 8px;
    font-size: 24px;
  }
  
  .error-state p {
    margin: 0 0 24px;
    color: var(--gray);
  }
  
  .breadcrumbs {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
    font-size: 14px;
  }
  
  .breadcrumbs a {
    color: var(--primary);
    text-decoration: none;
  }
  
  .breadcrumbs .material-icons {
    font-size: 16px;
    margin: 0 8px;
    color: var(--gray);
  }
  
  .page-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 24px;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .customer-header {
    display: flex;
    align-items: center;
    gap: 12px;
  }
  
  .page-title {
    margin: 0;
    font-size: 28px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .customer-type {
    margin: 4px 0 0;
    color: var(--gray);
    font-size: 14px;
  }
  
  .page-actions {
    display: flex;
    gap: 8px;
  }
  
  .badge {
    display: inline-block;
    padding: 4px 12px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
    text-transform: capitalize;
  }
  
  .badge-active {
    background-color: #e6f7e6;
    color: #2e7d32;
  }
  
  .badge-inactive {
    background-color: #f5f5f5;
    color: #757575;
  }
  
  /* Tab Styles */
  .tab-pane {
    display: none;
    padding: 24px 0;
  }
  
  .tab-pane.active {
    display: block;
  }
  
  /* Map styles */
  .map-container {
    width: 100%;
    height: 300px;
    background-color: #f3f3f3;
    border-radius: 8px;
    position: relative;
    margin-bottom: 12px;
    overflow: hidden;
  }
  
  .map-simulation {
    width: 100%;
    height: 100%;
    background-color: #e6f2ff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    background-image: linear-gradient(rgba(255,255,255,0.7), rgba(255,255,255,0.7)), 
                      repeating-linear-gradient(0deg, transparent, transparent 20px, #ccc 20px, #ccc 21px),
                      repeating-linear-gradient(90deg, transparent, transparent 20px, #ccc 20px, #ccc 21px);
  }
  
  .map-pin {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -100%);
    z-index: 10;
  }
  
  .map-icon, .map-pin .material-icons {
    color: #d32f2f;
    font-size: 36px;
    filter: drop-shadow(0 2px 5px rgba(0,0,0,0.3));
  }
  
  .map-info-window {
    padding: 8px;
    max-width: 250px;
    font-size: 14px;
    line-height: 1.4;
  }
  
  .map-error {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    text-align: center;
    padding: 20px;
  }
  
  .map-error p {
    margin: 10px 0;
    color: #666;
  }
  
  .map-address {
    background-color: white;
    padding: 8px 16px;
    border-radius: 4px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    margin-top: 40px;
    max-width: 80%;
    text-align: center;
  }
  
  .section-subheading {
    font-size: 18px;
    margin: 24px 0 16px;
    color: var(--dark);
    font-weight: 500;
  }
  
  .form-heading {
    font-size: 18px;
    margin: 0 0 16px;
    color: var(--primary);
    font-weight: 500;
  }
  
  .upload-form {
    background-color: #f9f9f9;
    padding: 16px;
    border-radius: 8px;
    margin-bottom: 16px;
  }
  
  .new-address-form {
    background-color: #f9f9f9;
    padding: 16px;
    border-radius: 8px;
    margin: 16px 0;
  }
  
  .stats-summary {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
    max-width: 95%;
    margin-left: auto;
    margin-right: auto;
  }
  
  .stat-card {
    background-color: white;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    padding: 16px;
    display: flex;
    align-items: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .stat-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 24px rgba(0, 0, 0, 0.12);
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
    color: var(--primary);
    font-size: 24px;
  }
  
  .stat-content {
    flex: 1;
  }
  
  .stat-value {
    margin: 0;
    font-size: 20px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .stat-label {
    margin: 4px 0 0;
    font-size: 12px;
    color: var(--gray);
  }
  
  .tabs-container {
    max-width: 95%;
    margin-left: auto;
    margin-right: auto;
    margin-bottom: 24px;
    overflow-x: auto;
  }
  
  .tabs {
    display: flex;
    border-bottom: 1px solid #e0e0e0;
  }
  
  .tab {
    padding: 12px 24px;
    border: none;
    background: none;
    font-size: 14px;
    font-weight: 500;
    color: var(--gray);
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    position: relative;
    transition: color 0.2s;
  }
  
  .tab:hover {
    color: var(--primary);
  }
  
  .tab.active {
    color: var(--primary);
  }
  
  .tab.active::after {
    content: '';
    position: absolute;
    bottom: -1px;
    left: 0;
    right: 0;
    height: 2px;
    background-color: var(--primary);
  }
  
  .tab-content {
    max-width: 95%;
    margin-left: auto;
    margin-right: auto;
  }
  
  .tab-pane {
    padding: 24px 0;
  }
  
  .section-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
    margin-bottom: 24px;
  }
  
  .card {
    background-color: #fff;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    margin-bottom: 24px;
    overflow: hidden;
  }
  
  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px 24px;
    border-bottom: 1px solid #f0f0f0;
  }
  
  .card-header h2 {
    margin: 0;
    font-size: 18px;
    font-weight: 600;
    color: var(--dark);
  }
  
  .card-body {
    padding: 24px;
  }
  
  .info-row {
    display: flex;
    margin-bottom: 16px;
  }
  
  .info-row:last-child {
    margin-bottom: 0;
  }
  
  .info-label {
    width: 160px;
    font-weight: 500;
    color: var(--gray);
  }
  
  .info-value {
    flex: 1;
  }
  
  .info-value a {
    color: var(--primary);
    text-decoration: none;
  }
  
  .form-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
  }
  
  .form-group {
    flex: 1 1 calc(50% - 8px);
    min-width: 200px;
  }
  
  .form-group.wide {
    flex: 1 1 100%;
  }
  
  .form-group label {
    display: block;
    margin-bottom: 8px;
    font-weight: 500;
  }
  
  .form-control {
    width: 100%;
    padding: 10px 12px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 14px;
  }
  
  textarea.form-control {
    resize: vertical;
    min-height: 100px;
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 8px;
    margin-top: 16px;
    width: 100%;
  }
  
  .table-responsive {
    overflow-x: auto;
  }
  
  .table {
    width: 100%;
    border-collapse: collapse;
  }
  
  .table th {
    text-align: left;
    padding: 12px 16px;
    font-weight: 500;
    color: var(--gray);
    border-bottom: 1px solid #e0e0e0;
  }
  
  .table td {
    padding: 12px 16px;
    border-bottom: 1px solid #f0f0f0;
    vertical-align: middle;
  }
  
  .status-badge {
    display: inline-block;
    padding: 4px 8px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
    text-transform: capitalize;
  }
  
  .status-badge.delivered,
  .status-badge.completed {
    background-color: #e6f7e6;
    color: #2e7d32;
  }
  
  .status-badge.processing {
    background-color: #e3f2fd;
    color: #1565c0;
  }
  
  .status-badge.shipped {
    background-color: #fff8e1;
    color: #f57f17;
  }
  
  .action-buttons {
    display: flex;
    gap: 4px;
  }
  
  .btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 8px 16px;
    border-radius: 4px;
    font-weight: 500;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
    border: none;
  }
  
  .btn-primary {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-primary:hover {
    background-color: var(--primary-dark);
  }
  
  .btn-outline-primary {
    background-color: transparent;
    border: 1px solid var(--primary);
    color: var(--primary);
  }
  
  .btn-outline-primary:hover {
    background-color: rgba(25, 118, 210, 0.05);
  }
  
  .btn-outline-secondary {
    background-color: transparent;
    border: 1px solid #ddd;
    color: var(--gray);
  }
  
  .btn-outline-secondary:hover {
    background-color: #f5f5f5;
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
  
  .btn-text {
    background: none;
    border: none;
    padding: 0;
    color: var(--primary);
    font-weight: 500;
    font-size: 14px;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    gap: 4px;
  }
  
  .btn-text:hover {
    text-decoration: underline;
  }
  
  .notes-content {
    white-space: pre-line;
    line-height: 1.5;
  }
  
  .header-actions {
    display: flex;
    gap: 16px;
    align-items: center;
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
  
  .filter-bar {
    display: flex;
    gap: 16px;
    margin-bottom: 16px;
  }
  
  .filter-group {
    display: flex;
    align-items: center;
    gap: 12px;
  }
  
  .filter-group label {
    font-size: 14px;
    color: var(--gray);
  }
  
  .form-select {
    height: 40px;
    padding: 0 12px;
    background-color: white;
    border: 1px solid var(--gray-light);
    border-radius: 4px;
    font-size: 14px;
    cursor: pointer;
  }
  
  .form-control:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 2px rgba(25, 118, 210, 0.2);
  }
  
  .notes-actions {
    display: flex;
    justify-content: flex-end;
    margin-top: 16px;
  }
  
  .filter-dropdown {
    min-width: 180px;
  }
  
  /* Timeline styles */
  .timeline {
    position: relative;
    padding-left: 32px;
  }
  
  .timeline::before {
    content: '';
    position: absolute;
    left: 15px;
    top: 0;
    bottom: 0;
    width: 2px;
    background-color: #e0e0e0;
  }
  
  .timeline-item {
    position: relative;
    margin-bottom: 32px;
  }
  
  .timeline-item:last-child {
    margin-bottom: 0;
  }
  
  .timeline-icon {
    position: absolute;
    left: -32px;
    top: 0;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    background-color: white;
    border: 2px solid #e0e0e0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1;
  }
  
  .timeline-icon .material-icons {
    font-size: 16px;
    color: var(--primary);
  }
  
  .timeline-content {
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }
  
  .timeline-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 8px;
  }
  
  .timeline-header h3, .timeline-header h4 {
    margin: 0;
    font-size: 16px;
    font-weight: 500;
  }
  
  .timeline-date {
    font-size: 12px;
    color: var(--gray);
  }
  
  .timeline-meta {
    margin-top: 8px;
    font-size: 13px;
    color: var(--gray);
  }
  
  .timeline-actions {
    margin-top: 12px;
    display: flex;
    gap: 8px;
  }
  
  /* Documents */
  .document-card {
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 16px;
    display: flex;
    flex-direction: column;
    transition: transform 0.2s, box-shadow 0.2s;
  }
  
  .document-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }
  
  .document-icon {
    width: 48px;
    height: 48px;
    border-radius: 8px;
    background-color: rgba(25, 118, 210, 0.1);
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 16px;
  }
  
  .document-icon .material-icons {
    color: var(--primary);
    font-size: 24px;
  }
  
  .document-name {
    margin: 0 0 8px;
    font-size: 16px;
    font-weight: 500;
  }
  
  .document-meta {
    margin: 0;
    font-size: 12px;
    color: var(--gray);
  }
  
  .document-actions {
    display: flex;
    justify-content: flex-end;
    margin-top: 16px;
    gap: 8px;
  }
  
  /* Address styles */
  .address-card {
    background-color: #f9f9f9;
    border-radius: 8px;
    padding: 16px;
    position: relative;
    margin-bottom: 16px;
  }
  
  .address-card.primary {
    border: 1px solid var(--primary);
  }
  
  .address-badge {
    position: absolute;
    top: 0;
    right: 0;
    background-color: var(--primary);
    color: white;
    font-size: 12px;
    padding: 4px 8px;
    border-radius: 0 8px 0 8px;
  }
  
  .address-line {
    margin: 0 0 8px;
    line-height: 1.5;
  }
  
  .address-actions {
    margin-top: 16px;
    display: flex;
    gap: 8px;
  }
  
  @media (max-width: 1200px) {
    .section-grid {
      grid-template-columns: 1fr;
    }
    
    .stats-summary {
      grid-template-columns: repeat(2, 1fr);
    }
  }
  
  @media (max-width: 768px) {
    .stats-summary {
      grid-template-columns: 1fr;
    }
    
    .page-header {
      flex-direction: column;
      align-items: flex-start;
    }
    
    .page-actions {
      width: 100%;
      justify-content: flex-start;
      flex-wrap: wrap;
    }
    
    .tab {
      padding: 12px 16px;
    }
    
    .document-filters {
      flex-direction: column;
      gap: 16px;
    }
    
    .search-box {
      width: 100%;
    }
  }
</style>
