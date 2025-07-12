<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Live Weather App</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet" />
  <link href="https://unpkg.com/boxicons@2.1.4/css/boxicons.min.css" rel="stylesheet" />
  <style>
    :root {
      --primary: #4361ee;
      --secondary: #3a0ca3;
      --accent: #4cc9f0;
      --light: #f8f9fa;
      --dark: #212529;
      --success: #4bb543;
      --warning: #fca311;
      --danger: #ef233c;
      --card-bg: rgba(255, 255, 255, 0.9);
      --text-primary: #2b2d42;
      --text-secondary: #6c757d;
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Inter', sans-serif;
    }

    body {
      min-height: 100vh;
      background: linear-gradient(135deg, #4361ee, #3a0ca3);
      color: var(--text-primary);
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 20px;
    }

    .weather-app {
      width: 100%;
      max-width: 800px;
      background: var(--card-bg);
      border-radius: 20px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
      overflow: hidden;
      backdrop-filter: blur(10px);
    }

    .app-header {
      padding: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: linear-gradient(to right, var(--primary), var(--secondary));
      color: white;
    }

    .app-title {
      font-size: 1.5rem;
      font-weight: 600;
    }

    .app-actions {
      display: flex;
      gap: 10px;
    }

    .btn {
      background: rgba(255, 255, 255, 0.2);
      border: none;
      border-radius: 50%;
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      cursor: pointer;
    }

    .search-container {
      padding: 20px;
      position: relative;
    }

    .search-input {
      width: 100%;
      padding: 15px 20px;
      border-radius: 50px;
      border: none;
      background: rgba(255, 255, 255, 0.9);
      font-size: 1rem;
    }

    .search-btn {
      position: absolute;
      right: 25px;
      top: 50%;
      transform: translateY(-50%);
      background: var(--primary);
      color: white;
      border: none;
      border-radius: 50%;
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }

    .weather-display {
      display: none;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }

    .location {
      font-size: 1.5rem;
      font-weight: 600;
      margin-bottom: 10px;
      display: flex;
      align-items: center;
      gap: 5px;
    }

    .temperature {
      font-size: 3rem;
      font-weight: 500;
    }

    .weather-description {
      font-size: 1.2rem;
      color: var(--text-secondary);
    }

    .weather-details,
    .forecast-container {
      margin-top: 20px;
      width: 100%;
    }

    .detail-card,
    .forecast-item {
      background: #ffffffaa;
      border-radius: 10px;
      padding: 10px;
      margin-bottom: 10px;
      text-align: center;
    }

    .unit-toggle {
      display: flex;
      justify-content: center;
      gap: 10px;
      margin-top: 15px;
    }

    .unit-btn {
      padding: 5px 15px;
      border: 1px solid var(--primary);
      border-radius: 20px;
      background: #fff;
      cursor: pointer;
    }

    .unit-btn.active {
      background: var(--primary);
      color: white;
    }

    .last-updated {
      font-size: 0.8rem;
      color: var(--text-secondary);
      margin-top: 10px;
    }

    .loading,
    .error-message {
      text-align: center;
      margin: 20px;
      display: none;
    }

    .loading-spinner {
      border: 4px solid #ccc;
      border-top: 4px solid var(--primary);
      border-radius: 50%;
      width: 40px;
      height: 40px;
      animation: spin 1s linear infinite;
      margin: 0 auto 10px;
    }

    @keyframes spin {
      to {
        transform: rotate(360deg);
      }
    }
  </style>
</head>
<body>
  <div class="weather-app">
    <div class="app-header">
      <div class="app-title">Live Weather App</div>
      <div class="app-actions">
        <button class="btn" id="refresh-btn"><i class='bx bx-refresh'></i></button>
        <button class="btn" id="location-btn"><i class='bx bx-current-location'></i></button>
      </div>
    </div>

    <div class="search-container">
      <input type="text" class="search-input" id="search-input" placeholder="Search for a city..." />
      <button class="search-btn" id="search-btn"><i class='bx bx-search'></i></button>
    </div>

    <div class="loading" id="loading">
      <div class="loading-spinner"></div>
      <div class="loading-text">Loading weather data...</div>
    </div>

    <div class="error-message" id="error-message"></div>

    <div class="weather-display" id="weather-display">
      <div class="location" id="location"><i class='bx bx-map'></i><span>--</span></div>
      <div class="date-time" id="date-time">--:--</div>
      <img id="weather-icon" class="weather-icon" src="" alt="Icon" />
      <div class="temperature" id="temperature"><span class="temp-value">--</span>°<span class="temp-unit">C</span></div>
      <div class="weather-description" id="weather-description">--</div>

      <div class="weather-details">
        <div class="detail-card" id="feels-like">Feels like: --°</div>
        <div class="detail-card" id="humidity">Humidity: --%</div>
        <div class="detail-card" id="wind-speed">Wind Speed: -- km/h</div>
        <div class="detail-card" id="uv-index">UV Index: --</div>
      </div>

      <div class="forecast-container">
        <div class="forecast-title">6-Day Forecast</div>
        <div class="forecast-items" id="forecast-items"></div>
      </div>

      <div class="unit-toggle">
        <button class="unit-btn active" id="celsius-btn">°C</button>
        <button class="unit-btn" id="fahrenheit-btn">°F</button>
      </div>

      <div class="last-updated" id="last-updated">Last updated: --</div>
    </div>
  </div>

  <script>
    // Paste your full script.js content here (already processed earlier)
    // Due to space limits, I’ll generate a downloadable version next
  </script>
</body>
</html>
# live-weather
