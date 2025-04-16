<script>
  import { onMount } from 'svelte';
  
  // Sample data - in a real app, this would come from an API
  let tasks = [
    {
      id: 1,
      title: 'Morning feed check - Batch 45',
      description: 'Verify feed levels and distribution in all feeders',
      assignedTo: 'John Doe',
      dueDate: '2023-11-16T08:00:00',
      priority: 'high',
      status: 'completed',
      category: 'daily'
    },
    {
      id: 2,
      title: 'Vaccination - Batch 46',
      description: 'Administer scheduled vaccines to all birds',
      assignedTo: 'Sarah Johnson',
      dueDate: '2023-11-16T10:00:00',
      priority: 'high',
      status: 'pending',
      category: 'health'
    },
    {
      id: 3,
      title: 'Water quality test',
      description: 'Test pH and chlorine levels in all water lines',
      assignedTo: 'Mike Wilson',
      dueDate: '2023-11-16T11:30:00',
      priority: 'medium',
      status: 'pending',
      category: 'maintenance'
    },
    {
      id: 4,
      title: 'Prepare Batch 44 for weighing',
      description: 'Set up weighing equipment and prepare sampling protocol',
      assignedTo: 'John Doe',
      dueDate: '2023-11-16T14:00:00',
      priority: 'medium',
      status: 'pending',
      category: 'daily'
    },
    {
      id: 5,
      title: 'Check ventilation systems',
      description: 'Inspect fans, inlets, and controllers for proper operation',
      assignedTo: 'Mike Wilson',
      dueDate: '2023-11-16T16:00:00',
      priority: 'low',
      status: 'pending',
      category: 'maintenance'
    },
    {
      id: 6,
      title: 'Restock medication supplies',
      description: 'Inventory and order necessary medications',
      assignedTo: 'Sarah Johnson',
      dueDate: '2023-11-17T09:00:00',
      priority: 'medium',
      status: 'pending',
      category: 'health'
    },
    {
      id: 7,
      title: 'Clean feed storage area',
      description: 'Remove old feed and sanitize storage bins',
      assignedTo: 'John Doe',
      dueDate: '2023-11-17T13:00:00',
      priority: 'low',
      status: 'pending',
      category: 'maintenance'
    },
    {
      id: 8,
      title: 'Update feed consumption records',
      description: 'Enter daily feed consumption data into system',
      assignedTo: 'Lisa Brown',
      dueDate: '2023-11-16T17:00:00',
      priority: 'medium',
      status: 'pending',
      category: 'admin'
    }
  ];
  
  let filterStatus = 'all';
  let filterPriority = 'all';
  let filterCategory = 'all';
  let filterAssignee = 'all';
  let searchQuery = '';
  let showNewTaskForm = false;
  let newTask = createEmptyTask();
  
  function createEmptyTask() {
    return {
      title: '',
      description: '',
      assignedTo: '',
      dueDate: '',
      priority: 'medium',
      status: 'pending',
      category: 'daily'
    };
  }
  
  function toggleTaskStatus(id) {
    tasks = tasks.map(task => {
      if (task.id === id) {
        const newStatus = task.status === 'completed' ? 'pending' : 'completed';
        return { ...task, status: newStatus };
      }
      return task;
    });
  }
  
  function formatDate(dateString) {
    const date = new Date(dateString);
    return new Intl.DateTimeFormat('en-US', { 
      month: 'short', 
      day: 'numeric',
      hour: 'numeric',
      minute: 'numeric',
      hour12: true
    }).format(date);
  }
  
  function isOverdue(dateString) {
    const dueDate = new Date(dateString);
    const now = new Date();
    return dueDate < now && dateString;
  }
  
  function addNewTask() {
    if (newTask.title.trim() === '') return;
    
    const taskId = Math.max(0, ...tasks.map(t => t.id)) + 1;
    tasks = [
      ...tasks,
      {
        id: taskId,
        ...newTask
      }
    ];
    
    showNewTaskForm = false;
    newTask = createEmptyTask();
  }
  
  function cancelNewTask() {
    showNewTaskForm = false;
    newTask = createEmptyTask();
  }
  
  // Filter tasks based on selected filters and search query
  $: filteredTasks = tasks.filter(task => {
    if (filterStatus !== 'all' && task.status !== filterStatus) return false;
    if (filterPriority !== 'all' && task.priority !== filterPriority) return false;
    if (filterCategory !== 'all' && task.category !== filterCategory) return false;
    if (filterAssignee !== 'all' && task.assignedTo !== filterAssignee) return false;
    if (searchQuery && !task.title.toLowerCase().includes(searchQuery.toLowerCase())) return false;
    return true;
  });
  
  // Get unique assignees for filter dropdown
  $: assignees = [...new Set(tasks.map(task => task.assignedTo))];
</script>

<svelte:head>
  <title>Tasks | BroilerPro</title>
</svelte:head>

<div class="tasks-page">
  <div class="page-header">
    <div>
      <h1 class="page-title">Tasks</h1>
      <p class="page-subtitle">Manage and track farm activities</p>
    </div>
    
    <div class="page-actions">
      <button class="btn btn-primary" on:click={() => showNewTaskForm = true}>
        <span class="material-icons">add</span>
        New Task
      </button>
    </div>
  </div>
  
  <div class="filter-bar">
    <div class="search-box">
      <span class="material-icons search-icon">search</span>
      <input 
        type="text" 
        placeholder="Search tasks..." 
        bind:value={searchQuery}
      />
    </div>
    
    <div class="filter-actions">
      <div class="filter-group">
        <label>Status:</label>
        <select class="form-select" bind:value={filterStatus}>
          <option value="all">All Statuses</option>
          <option value="pending">Pending</option>
          <option value="completed">Completed</option>
        </select>
      </div>
      
      <div class="filter-group">
        <label>Priority:</label>
        <select class="form-select" bind:value={filterPriority}>
          <option value="all">All Priorities</option>
          <option value="high">High</option>
          <option value="medium">Medium</option>
          <option value="low">Low</option>
        </select>
      </div>
      
      <div class="filter-group">
        <label>Category:</label>
        <select class="form-select" bind:value={filterCategory}>
          <option value="all">All Categories</option>
          <option value="daily">Daily Operations</option>
          <option value="health">Health & Vaccination</option>
          <option value="maintenance">Maintenance</option>
          <option value="admin">Administrative</option>
        </select>
      </div>
      
      <div class="filter-group">
        <label>Assignee:</label>
        <select class="form-select" bind:value={filterAssignee}>
          <option value="all">All Assignees</option>
          {#each assignees as assignee}
            <option value={assignee}>{assignee}</option>
          {/each}
        </select>
      </div>
    </div>
  </div>
  
  {#if showNewTaskForm}
    <div class="card new-task-form">
      <div class="card-header">
        <h2 class="card-title">New Task</h2>
        <button class="btn-icon" on:click={cancelNewTask}>
          <span class="material-icons">close</span>
        </button>
      </div>
      <div class="card-body">
        <form on:submit|preventDefault={addNewTask}>
          <div class="form-group">
            <label for="taskTitle">Title</label>
            <input 
              type="text" 
              id="taskTitle" 
              class="form-control" 
              placeholder="Task title" 
              bind:value={newTask.title}
              required
            />
          </div>
          
          <div class="form-group">
            <label for="taskDescription">Description</label>
            <textarea 
              id="taskDescription" 
              class="form-control" 
              placeholder="Task description" 
              bind:value={newTask.description}
              rows="3"
            ></textarea>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label for="taskAssignee">Assigned To</label>
              <input 
                type="text" 
                id="taskAssignee" 
                class="form-control" 
                placeholder="Assignee name" 
                bind:value={newTask.assignedTo}
              />
            </div>
            
            <div class="form-group">
              <label for="taskDueDate">Due Date</label>
              <input 
                type="datetime-local" 
                id="taskDueDate" 
                class="form-control" 
                bind:value={newTask.dueDate}
              />
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label for="taskPriority">Priority</label>
              <select id="taskPriority" class="form-control" bind:value={newTask.priority}>
                <option value="high">High</option>
                <option value="medium">Medium</option>
                <option value="low">Low</option>
              </select>
            </div>
            
            <div class="form-group">
              <label for="taskCategory">Category</label>
              <select id="taskCategory" class="form-control" bind:value={newTask.category}>
                <option value="daily">Daily Operations</option>
                <option value="health">Health & Vaccination</option>
                <option value="maintenance">Maintenance</option>
                <option value="admin">Administrative</option>
              </select>
            </div>
          </div>
          
          <div class="form-actions">
            <button type="button" class="btn btn-outline-secondary" on:click={cancelNewTask}>
              Cancel
            </button>
            <button type="submit" class="btn btn-primary">
              Add Task
            </button>
          </div>
        </form>
      </div>
    </div>
  {/if}
  
  <div class="tasks-list">
    {#if filteredTasks.length === 0}
      <div class="card">
        <div class="empty-state">
          <span class="material-icons">assignment_late</span>
          <p>No tasks found matching your criteria</p>
          <button class="btn btn-outline-primary" on:click={() => { 
            filterStatus = 'all'; 
            filterPriority = 'all'; 
            filterCategory = 'all'; 
            filterAssignee = 'all'; 
            searchQuery = ''; 
          }}>
            Clear Filters
          </button>
        </div>
      </div>
    {:else}
      {#each filteredTasks as task}
        <div class="task-card" class:completed={task.status === 'completed'}>
          <div class="task-checkbox">
            <label class="checkbox-container">
              <input 
                type="checkbox" 
                checked={task.status === 'completed'} 
                on:change={() => toggleTaskStatus(task.id)}
              />
              <span class="checkmark"></span>
            </label>
          </div>
          
          <div class="task-content">
            <div class="task-header">
              <h3 class="task-title">{task.title}</h3>
              <div class="task-badges">
                <span class="priority-badge priority-{task.priority}">
                  {task.priority}
                </span>
                <span class="category-badge category-{task.category}">
                  {task.category}
                </span>
              </div>
            </div>
            
            {#if task.description}
              <p class="task-description">{task.description}</p>
            {/if}
            
            <div class="task-meta">
              <div class="meta-item">
                <span class="material-icons">person</span>
                <span>{task.assignedTo}</span>
              </div>
              
              <div class="meta-item" class:overdue={isOverdue(task.dueDate) && task.status !== 'completed'}>
                <span class="material-icons">schedule</span>
                <span>{formatDate(task.dueDate)}</span>
              </div>
            </div>
          </div>
          
          <div class="task-actions">
            <button class="btn-icon" title="Edit Task">
              <span class="material-icons">edit</span>
            </button>
            <button class="btn-icon" title="Delete Task">
              <span class="material-icons">delete</span>
            </button>
          </div>
        </div>
      {/each}
    {/if}
  </div>
</div>

<style>
  .tasks-page {
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
  
  .new-task-form {
    margin-bottom: 16px;
  }
  
  .form-group {
    margin-bottom: 16px;
  }
  
  .form-row {
    display: flex;
    gap: 16px;
    margin-bottom: 16px;
  }
  
  .form-row .form-group {
    flex: 1;
    margin-bottom: 0;
  }
  
  .form-control {
    width: 100%;
    padding: 8px 12px;
    border: 1px solid var(--gray-light);
    border-radius: 4px;
    font-size: 14px;
  }
  
  .form-control:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 2px rgba(25, 118, 210, 0.1);
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 8px;
  }
  
  .tasks-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }
  
  .task-card {
    display: flex;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 16px;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }
  
  .task-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  .task-card.completed {
    background-color: #f9f9f9;
    opacity: 0.8;
  }
  
  .task-checkbox {
    margin-right: 16px;
    padding-top: 2px;
  }
  
  .checkbox-container {
    display: block;
    position: relative;
    width: 20px;
    height: 20px;
    cursor: pointer;
  }
  
  .checkbox-container input {
    position: absolute;
    opacity: 0;
    cursor: pointer;
    height: 0;
    width: 0;
  }
  
  .checkmark {
    position: absolute;
    top: 0;
    left: 0;
    height: 20px;
    width: 20px;
    background-color: white;
    border: 2px solid var(--gray-light);
    border-radius: 4px;
    transition: all 0.2s ease;
  }
  
  .checkbox-container:hover input ~ .checkmark {
    border-color: var(--primary);
  }
  
  .checkbox-container input:checked ~ .checkmark {
    background-color: var(--primary);
    border-color: var(--primary);
  }
  
  .checkmark:after {
    content: "";
    position: absolute;
    display: none;
  }
  
  .checkbox-container input:checked ~ .checkmark:after {
    display: block;
  }
  
  .checkbox-container .checkmark:after {
    left: 6px;
    top: 2px;
    width: 5px;
    height: 10px;
    border: solid white;
    border-width: 0 2px 2px 0;
    transform: rotate(45deg);
  }
  
  .task-content {
    flex: 1;
  }
  
  .task-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 8px;
    flex-wrap: wrap;
    gap: 8px;
  }
  
  .task-title {
    font-size: 16px;
    font-weight: 500;
    margin: 0;
    color: var(--dark);
  }
  
  .task-card.completed .task-title {
    text-decoration: line-through;
    color: var(--gray);
  }
  
  .task-badges {
    display: flex;
    gap: 8px;
  }
  
  .priority-badge,
  .category-badge {
    font-size: 11px;
    text-transform: uppercase;
    font-weight: 500;
    padding: 2px 6px;
    border-radius: 4px;
  }
  
  .priority-high {
    background-color: rgba(244, 67, 54, 0.1);
    color: var(--danger);
  }
  
  .priority-medium {
    background-color: rgba(255, 152, 0, 0.1);
    color: var(--warning);
  }
  
  .priority-low {
    background-color: rgba(76, 175, 80, 0.1);
    color: var(--success);
  }
  
  .category-daily {
    background-color: rgba(25, 118, 210, 0.1);
    color: var(--primary);
  }
  
  .category-health {
    background-color: rgba(156, 39, 176, 0.1);
    color: #9c27b0;
  }
  
  .category-maintenance {
    background-color: rgba(96, 125, 139, 0.1);
    color: #607d8b;
  }
  
  .category-admin {
    background-color: rgba(33, 150, 243, 0.1);
    color: var(--info);
  }
  
  .task-description {
    font-size: 14px;
    color: var(--gray);
    margin: 0 0 12px;
  }
  
  .task-card.completed .task-description {
    color: var(--gray);
  }
  
  .task-meta {
    display: flex;
    gap: 16px;
  }
  
  .meta-item {
    display: flex;
    align-items: center;
    font-size: 12px;
    color: var(--gray);
  }
  
  .meta-item.overdue {
    color: var(--danger);
  }
  
  .meta-item .material-icons {
    font-size: 14px;
    margin-right: 4px;
  }
  
  .task-actions {
    display: flex;
    gap: 8px;
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
    .filter-bar {
      flex-direction: column;
      align-items: stretch;
    }
    
    .search-box {
      width: 100%;
    }
    
    .form-row {
      flex-direction: column;
      gap: 16px;
    }
  }
  
  @media (max-width: 768px) {
    .page-header {
      flex-direction: column;
      align-items: flex-start;
    }
    
    .filter-actions {
      flex-direction: column;
      align-items: stretch;
      width: 100%;
    }
    
    .filter-group {
      flex-direction: column;
      align-items: flex-start;
      width: 100%;
    }
    
    .form-select {
      width: 100%;
    }
    
    .task-header {
      flex-direction: column;
    }
    
    .task-meta {
      flex-direction: column;
      gap: 8px;
    }
  }
</style>
