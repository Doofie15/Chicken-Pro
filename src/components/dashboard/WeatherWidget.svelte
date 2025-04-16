<script>
  import { onMount } from 'svelte';
  
  // Sample data - in a real app, this would come from a weather API
  const weatherData = {
    current: {
      temp: 28,
      humidity: 65,
      condition: 'Partly Cloudy',
      icon: 'cloud'
    },
    forecast: [
      { day: 'Today', temp: 28, icon: 'cloud' },
      { day: 'Thu', temp: 30, icon: 'wb_sunny' },
      { day: 'Fri', temp: 29, icon: 'wb_sunny' },
      { day: 'Sat', temp: 27, icon: 'grain' },
      { day: 'Sun', temp: 26, icon: 'thunderstorm' }
    ],
    farmConditions: {
      house1: { temp: 26.5, humidity: 62, status: 'optimal' },
      house2: { temp: 29.2, humidity: 68, status: 'warning' },
      house3: { temp: 26.8, humidity: 63, status: 'optimal' },
      house4: { temp: 27.1, humidity: 64, status: 'optimal' }
    }
  };
  
  let selectedHouse = 'house1';
  
  function getStatusColor(status) {
    if (status === 'optimal') return 'var(--success)';
    if (status === 'warning') return 'var(--warning)';
    if (status === 'danger') return 'var(--danger)';
    return 'var(--gray)';
  }
  
  function selectHouse(house) {
    selectedHouse = house;
  }
</script>

<div class="weather-widget">
  <div class="current-weather">
    <div class="weather-icon">
      <span class="material-icons">{weatherData.current.icon}</span>
    </div>
    <div class="weather-info">
      <div class="weather-temp">{weatherData.current.temp}°C</div>
      <div class="weather-condition">{weatherData.current.condition}</div>
      <div class="weather-humidity">
        <span class="material-icons">water_drop</span>
        {weatherData.current.humidity}% Humidity
      </div>
    </div>
  </div>
  
  <div class="weather-forecast">
    {#each weatherData.forecast as day}
      <div class="forecast-day">
        <div class="forecast-name">{day.day}</div>
        <div class="forecast-icon">
          <span class="material-icons">{day.icon}</span>
        </div>
        <div class="forecast-temp">{day.temp}°</div>
      </div>
    {/each}
  </div>
  
  <div class="farm-conditions">
    <h3 class="conditions-title">Farm Conditions</h3>
    
    <div class="house-selector">
      {#each Object.keys(weatherData.farmConditions) as house}
        <button 
          class="house-btn" 
          class:active={selectedHouse === house}
          on:click={() => selectHouse(house)}
        >
          <span 
            class="house-status" 
            style="background-color: {getStatusColor(weatherData.farmConditions[house].status)}"
          ></span>
          {house.replace('house', 'House ')}
        </button>
      {/each}
    </div>
    
    <div class="house-details">
      <div class="detail-item">
        <span class="detail-label">Temperature</span>
        <span 
          class="detail-value" 
          style="color: {getStatusColor(weatherData.farmConditions[selectedHouse].status)}"
        >
          {weatherData.farmConditions[selectedHouse].temp}°C
        </span>
      </div>
      
      <div class="detail-item">
        <span class="detail-label">Humidity</span>
        <span class="detail-value">
          {weatherData.farmConditions[selectedHouse].humidity}%
        </span>
      </div>
      
      <div class="detail-item">
        <span class="detail-label">Status</span>
        <span 
          class="detail-status"
          style="background-color: {getStatusColor(weatherData.farmConditions[selectedHouse].status)}"
        >
          {weatherData.farmConditions[selectedHouse].status}
        </span>
      </div>
    </div>
  </div>
</div>

<style>
  .weather-widget {
    width: 100%;
  }
  
  .current-weather {
    display: flex;
    align-items: center;
    margin-bottom: 16px;
    padding-bottom: 16px;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .weather-icon {
    width: 60px;
    height: 60px;
    background-color: rgba(33, 150, 243, 0.1);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 16px;
  }
  
  .weather-icon .material-icons {
    font-size: 32px;
    color: var(--info);
  }
  
  .weather-info {
    flex: 1;
  }
  
  .weather-temp {
    font-size: 24px;
    font-weight: 500;
    color: var(--dark);
    margin-bottom: 4px;
  }
  
  .weather-condition {
    font-size: 14px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .weather-humidity {
    font-size: 12px;
    color: var(--gray);
    display: flex;
    align-items: center;
  }
  
  .weather-humidity .material-icons {
    font-size: 14px;
    margin-right: 4px;
    color: var(--info);
  }
  
  .weather-forecast {
    display: flex;
    justify-content: space-between;
    margin-bottom: 16px;
    padding-bottom: 16px;
    border-bottom: 1px solid var(--gray-light);
  }
  
  .forecast-day {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 1;
  }
  
  .forecast-name {
    font-size: 12px;
    color: var(--gray);
    margin-bottom: 4px;
  }
  
  .forecast-icon {
    margin-bottom: 4px;
  }
  
  .forecast-icon .material-icons {
    font-size: 20px;
    color: var(--info);
  }
  
  .forecast-temp {
    font-size: 14px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .farm-conditions {
    margin-top: 16px;
  }
  
  .conditions-title {
    font-size: 14px;
    font-weight: 500;
    margin: 0 0 12px;
  }
  
  .house-selector {
    display: flex;
    gap: 8px;
    margin-bottom: 16px;
    overflow-x: auto;
    padding-bottom: 4px;
  }
  
  .house-btn {
    display: flex;
    align-items: center;
    background-color: white;
    border: 1px solid var(--gray-light);
    border-radius: 4px;
    padding: 6px 10px;
    font-size: 12px;
    cursor: pointer;
    transition: all 0.2s ease;
    white-space: nowrap;
  }
  
  .house-btn:hover {
    border-color: var(--primary);
  }
  
  .house-btn.active {
    background-color: var(--primary);
    border-color: var(--primary);
    color: white;
  }
  
  .house-status {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    margin-right: 6px;
  }
  
  .house-details {
    background-color: var(--gray-light);
    border-radius: 8px;
    padding: 12px;
  }
  
  .detail-item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
  }
  
  .detail-item:last-child {
    margin-bottom: 0;
  }
  
  .detail-label {
    font-size: 12px;
    color: var(--gray);
  }
  
  .detail-value {
    font-size: 12px;
    font-weight: 500;
    color: var(--dark);
  }
  
  .detail-status {
    font-size: 11px;
    text-transform: uppercase;
    font-weight: 500;
    padding: 2px 6px;
    border-radius: 4px;
    color: white;
  }
</style>
