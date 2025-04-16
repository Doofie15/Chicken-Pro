<script>
  import { onMount } from 'svelte';
  
  // Sample data - in a real app, this would come from an API
  const settings = {
    farm: {
      name: 'Sunrise Poultry Farm',
      location: '123 Farm Road, Countryside',
      capacity: 25000,
      establishedYear: 2010
    },
    notifications: {
      email: true,
      sms: true,
      push: false,
      dailyReports: true,
      alertsOnly: false
    },
    users: [
      {
        id: 1,
        name: 'Farm Manager',
        email: 'manager@sunrisepoultry.com',
        role: 'Administrator',
        lastActive: '2025-04-16T10:30:00',
        image: 'https://randomuser.me/api/portraits/men/1.jpg'
      },
      {
        id: 2,
        name: 'John Smith',
        email: 'john@sunrisepoultry.com',
        role: 'Farm Worker',
        lastActive: '2025-04-16T09:15:00',
        image: 'https://randomuser.me/api/portraits/men/2.jpg'
      },
      {
        id: 3,
        name: 'Sarah Johnson',
        email: 'sarah@sunrisepoultry.com',
        role: 'Veterinarian',
        lastActive: '2025-04-15T16:45:00',
        image: 'https://randomuser.me/api/portraits/women/3.jpg'
      }
    ],
    integrations: [
      { name: 'Accounting Software', status: 'Connected', lastSync: '2025-04-16T08:00:00' },
      { name: 'Weather Service', status: 'Connected', lastSync: '2025-04-16T07:30:00' },
      { name: 'Feed Supplier Portal', status: 'Disconnected', lastSync: '2025-04-10T14:20:00' }
    ]
  };
  
  let activeTab = 'farm';
  
  function setActiveTab(tab) {
    activeTab = tab;
  }
</script>

<svelte:head>
  <title>Settings | BroilerPro</title>
</svelte:head>

<div class="settings-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Settings</h1>
      <p class="page-subtitle">Configure your farm and application preferences</p>
    </div>
  </div>
  
  <div class="settings-container">
    <div class="settings-sidebar">
      <ul class="settings-tabs">
        <li class="settings-tab" class:active={activeTab === 'farm'}>
          <button on:click={() => setActiveTab('farm')}>
            <span class="material-icons">home_work</span>
            <span>Farm Profile</span>
          </button>
        </li>
        <li class="settings-tab" class:active={activeTab === 'notifications'}>
          <button on:click={() => setActiveTab('notifications')}>
            <span class="material-icons">notifications</span>
            <span>Notifications</span>
          </button>
        </li>
        <li class="settings-tab" class:active={activeTab === 'users'}>
          <button on:click={() => setActiveTab('users')}>
            <span class="material-icons">people</span>
            <span>Users & Permissions</span>
          </button>
        </li>
        <li class="settings-tab" class:active={activeTab === 'integrations'}>
          <button on:click={() => setActiveTab('integrations')}>
            <span class="material-icons">integration_instructions</span>
            <span>Integrations</span>
          </button>
        </li>
      </ul>
    </div>
    
    <div class="settings-content">
      {#if activeTab === 'farm'}
        <div class="settings-section">
          <h2 class="section-title">Farm Profile</h2>
          
          <div class="card">
            <div class="form-group">
              <label for="farmName">Farm Name</label>
              <input type="text" id="farmName" value={settings.farm.name} class="form-control" />
            </div>
            
            <div class="form-group">
              <label for="farmLocation">Location</label>
              <input type="text" id="farmLocation" value={settings.farm.location} class="form-control" />
            </div>
            
            <div class="form-group">
              <label for="farmCapacity">Capacity (birds)</label>
              <input type="number" id="farmCapacity" value={settings.farm.capacity} class="form-control" />
            </div>
            
            <div class="form-group">
              <label for="establishedYear">Established Year</label>
              <input type="number" id="establishedYear" value={settings.farm.establishedYear} class="form-control" />
            </div>
            
            <div class="form-actions">
              <button class="btn btn-primary">Save Changes</button>
            </div>
          </div>
        </div>
      {:else if activeTab === 'notifications'}
        <div class="settings-section">
          <h2 class="section-title">Notification Preferences</h2>
          
          <div class="card">
            <div class="form-check">
              <input type="checkbox" id="emailNotifications" checked={settings.notifications.email} class="form-check-input" />
              <label for="emailNotifications" class="form-check-label">Email Notifications</label>
            </div>
            
            <div class="form-check">
              <input type="checkbox" id="smsNotifications" checked={settings.notifications.sms} class="form-check-input" />
              <label for="smsNotifications" class="form-check-label">SMS Notifications</label>
            </div>
            
            <div class="form-check">
              <input type="checkbox" id="pushNotifications" checked={settings.notifications.push} class="form-check-input" />
              <label for="pushNotifications" class="form-check-label">Push Notifications</label>
            </div>
            
            <div class="form-check">
              <input type="checkbox" id="dailyReports" checked={settings.notifications.dailyReports} class="form-check-input" />
              <label for="dailyReports" class="form-check-label">Daily Reports</label>
            </div>
            
            <div class="form-check">
              <input type="checkbox" id="alertsOnly" checked={settings.notifications.alertsOnly} class="form-check-input" />
              <label for="alertsOnly" class="form-check-label">Alerts Only</label>
            </div>
            
            <div class="form-actions">
              <button class="btn btn-primary">Save Changes</button>
            </div>
          </div>
        </div>
      {:else if activeTab === 'users'}
        <div class="settings-section">
          <h2 class="section-title">Users & Permissions</h2>
          
          <div class="card">
            <div class="table-responsive">
              <table class="table">
                <thead>
                  <tr>
                    <th>User</th>
                    <th>Email</th>
                    <th>Role</th>
                    <th>Last Active</th>
                    <th>Actions</th>
                  </tr>
                </thead>
                <tbody>
                  {#each settings.users as user}
                    <tr>
                      <td>
                        <div class="user-cell">
                          <div class="user-avatar">
                            <img src={user.image} alt={user.name} />
                          </div>
                          <span>{user.name}</span>
                        </div>
                      </td>
                      <td>{user.email}</td>
                      <td>{user.role}</td>
                      <td>{new Date(user.lastActive).toLocaleString()}</td>
                      <td>
                        <button class="btn btn-sm btn-outline-primary">Edit</button>
                      </td>
                    </tr>
                  {/each}
                </tbody>
              </table>
            </div>
            
            <div class="form-actions">
              <button class="btn btn-primary">Add User</button>
            </div>
          </div>
        </div>
      {:else if activeTab === 'integrations'}
        <div class="settings-section">
          <h2 class="section-title">Integrations</h2>
          
          <div class="card">
            <div class="integrations-list">
              {#each settings.integrations as integration}
                <div class="integration-item">
                  <div class="integration-info">
                    <h3 class="integration-name">{integration.name}</h3>
                    <div class="integration-status" class:connected={integration.status === 'Connected'}>
                      {integration.status}
                    </div>
                    <div class="integration-sync">
                      Last sync: {new Date(integration.lastSync).toLocaleString()}
                    </div>
                  </div>
                  <div class="integration-actions">
                    {#if integration.status === 'Connected'}
                      <button class="btn btn-sm btn-outline-danger">Disconnect</button>
                    {:else}
                      <button class="btn btn-sm btn-primary">Connect</button>
                    {/if}
                  </div>
                </div>
              {/each}
            </div>
            
            <div class="form-actions">
              <button class="btn btn-primary">Add Integration</button>
            </div>
          </div>
        </div>
      {/if}
    </div>
  </div>
</div>

<style>
  .settings-page {
    padding: 16px;
  }
  
  .page-title {
    margin: 0;
    font-size: 24px;
    font-weight: 500;
  }
  
  .page-subtitle {
    margin: 4px 0 0;
    color: var(--gray);
    font-size: 14px;
  }
  
  .settings-container {
    display: flex;
    margin-top: 24px;
    gap: 24px;
  }
  
  .settings-sidebar {
    width: 240px;
    flex-shrink: 0;
  }
  
  .settings-content {
    flex: 1;
  }
  
  .settings-tabs {
    list-style: none;
    padding: 0;
    margin: 0;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  }
  
  .settings-tab button {
    display: flex;
    align-items: center;
    width: 100%;
    padding: 12px 16px;
    border: none;
    background: none;
    text-align: left;
    cursor: pointer;
    transition: background-color 0.2s;
  }
  
  .settings-tab button:hover {
    background-color: rgba(25, 118, 210, 0.05);
  }
  
  .settings-tab.active button {
    background-color: rgba(25, 118, 210, 0.1);
    color: var(--primary);
  }
  
  .settings-tab .material-icons {
    margin-right: 12px;
    font-size: 20px;
  }
  
  .section-title {
    margin: 0 0 16px;
    font-size: 18px;
    font-weight: 500;
  }
  
  .card {
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
    padding: 24px;
  }
  
  .form-group {
    margin-bottom: 16px;
  }
  
  .form-group label {
    display: block;
    margin-bottom: 8px;
    font-weight: 500;
  }
  
  .form-control {
    width: 100%;
    padding: 8px 12px;
    border: 1px solid var(--gray-light);
    border-radius: 4px;
    font-size: 14px;
  }
  
  .form-check {
    display: flex;
    align-items: center;
    margin-bottom: 12px;
  }
  
  .form-check-input {
    margin-right: 8px;
  }
  
  .form-actions {
    margin-top: 24px;
    display: flex;
    justify-content: flex-end;
  }
  
  .btn {
    padding: 8px 16px;
    border-radius: 4px;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    border: none;
  }
  
  .btn-primary {
    background-color: var(--primary);
    color: white;
  }
  
  .btn-outline-primary {
    background-color: transparent;
    border: 1px solid var(--primary);
    color: var(--primary);
  }
  
  .btn-outline-danger {
    background-color: transparent;
    border: 1px solid var(--danger);
    color: var(--danger);
  }
  
  .btn-sm {
    padding: 4px 8px;
    font-size: 12px;
  }
  
  .table {
    width: 100%;
    border-collapse: collapse;
  }
  
  .table th, .table td {
    padding: 12px;
    text-align: left;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .user-cell {
    display: flex;
    align-items: center;
  }
  
  .user-avatar {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    overflow: hidden;
    margin-right: 8px;
  }
  
  .user-avatar img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .integrations-list {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  .integration-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 16px;
    border: 1px solid var(--gray-light);
    border-radius: 8px;
  }
  
  .integration-name {
    margin: 0 0 8px;
    font-size: 16px;
    font-weight: 500;
  }
  
  .integration-status {
    font-size: 14px;
    color: var(--danger);
    margin-bottom: 4px;
  }
  
  .integration-status.connected {
    color: var(--success);
  }
  
  .integration-sync {
    font-size: 12px;
    color: var(--gray);
  }
  
  @media (max-width: 767px) {
    .settings-container {
      flex-direction: column;
    }
    
    .settings-sidebar {
      width: 100%;
    }
    
    .settings-tabs {
      display: flex;
      overflow-x: auto;
    }
    
    .settings-tab {
      flex-shrink: 0;
    }
  }
</style>
