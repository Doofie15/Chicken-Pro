<script>
  export let tasks = [];
  
  function toggleTask(id) {
    tasks = tasks.map(task => {
      if (task.id === id) {
        return { ...task, completed: !task.completed };
      }
      return task;
    });
  }
</script>

<div class="task-list">
  {#each tasks as task}
    <div class="task-item" class:completed={task.completed}>
      <div class="task-checkbox">
        <label class="checkbox-container">
          <input 
            type="checkbox" 
            checked={task.completed} 
            on:change={() => toggleTask(task.id)}
          />
          <span class="checkmark"></span>
        </label>
      </div>
      
      <div class="task-content">
        <h3 class="task-title">{task.title}</h3>
        <div class="task-meta">
          <span class="task-due">
            <span class="material-icons">schedule</span>
            {task.due}
          </span>
          <span class="task-priority priority-{task.priority}">
            {task.priority}
          </span>
        </div>
      </div>
      
      <div class="task-actions">
        <button class="btn-icon">
          <span class="material-icons">more_vert</span>
        </button>
      </div>
    </div>
  {/each}
</div>

<style>
  .task-list {
    width: 100%;
  }
  
  .task-item {
    display: flex;
    align-items: flex-start;
    padding: 16px;
    border-bottom: 1px solid var(--gray-light);
    transition: background-color 0.2s ease;
  }
  
  .task-item:last-child {
    border-bottom: none;
  }
  
  .task-item:hover {
    background-color: rgba(0, 0, 0, 0.02);
  }
  
  .task-item.completed .task-title {
    text-decoration: line-through;
    color: var(--gray);
  }
  
  .task-checkbox {
    margin-right: 12px;
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
  
  .task-title {
    margin: 0 0 4px;
    font-size: 14px;
    font-weight: 500;
  }
  
  .task-meta {
    display: flex;
    align-items: center;
    gap: 12px;
  }
  
  .task-due {
    font-size: 12px;
    color: var(--gray);
    display: flex;
    align-items: center;
  }
  
  .task-due .material-icons {
    font-size: 14px;
    margin-right: 4px;
  }
  
  .task-priority {
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
  
  .task-actions {
    margin-left: 8px;
  }
  
  .btn-icon {
    width: 28px;
    height: 28px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    cursor: pointer;
    color: var(--gray);
    transition: background-color 0.2s ease;
  }
  
  .btn-icon:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: var(--dark);
  }
</style>
