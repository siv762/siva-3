<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Weather App</title>
    <style>
      body {
        font-family: "Poppins", Arial, sans-serif;
        text-align: center;
        background: linear-gradient(135deg, #6dd5ed, #2193b0);
        color: #fff;
        margin: 0;
        padding: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
        overflow: hidden;
      }

      h1 {
        font-size: 2.5rem;
        margin-bottom: 20px;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
      }

      input,
      button {
        padding: 12px 20px;
        font-size: 1rem;
        border: none;
        border-radius: 25px;
        margin: 10px;
        outline: none;
      }

      input {
        width: 250px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      }

      button {
        background-color: #ff5722;
        color: #fff;
        cursor: pointer;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
        transition: all 0.3s ease-in-out;
      }

      button:hover {
        background-color: #e64a19;
        transform: scale(1.05);
      }

      .weather-info {
        margin-top: 20px;
        padding: 20px;
        background: rgba(255, 255, 255, 0.8);
        border-radius: 12px;
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
        display: inline-block;
        color: #333;
        width: 300px;
        text-align: left;
      }

      .weather-info h2 {
        font-size: 1.5rem;
        margin-bottom: 10px;
        color: #ff5722;
      }

      .weather-info p {
        margin: 5px 0;
        font-size: 1rem;
      }

      .weather-info .date {
        font-size: 0.9rem;
        color: #555;
      }
    </style>
  </head>
  <body>
    <h1>Weather App</h1>
    <input type="text" id="city" placeholder="Enter city name" />
    <div>
      <button onclick="changeDate(-1)">Previous Day</button>
      <button onclick="getWeather()">Get Weather</button>
      <button onclick="changeDate(1)">Next Day</button>
    </div>
    <div id="weather" class="weather-info" style="display: none"></div>

    <script>
      const apiKey = "dccb5a5aee63a53291169af302af7486";
      const forecastUrl = "https://api.openweathermap.org/data/2.5/forecast";
      let currentDate = new Date();

      function formatDate(date) {
        return date.toLocaleDateString("en-GB", {
          weekday: "long",
          year: "numeric",
          month: "long",
          day: "numeric",
        });
      }

      function changeDate(offset) {
        currentDate.setDate(currentDate.getDate() + offset);
        getWeather();
      }

      async function getWeather() {
        const city = document.getElementById("city").value;
        const weatherDiv = document.getElementById("weather");

        if (!city) {
          alert("Please enter a city name!");
          return;
        }

        const url = `${forecastUrl}?q=${city}&appid=${apiKey}&units=metric`;

        try {
          const response = await fetch(url);
          if (!response.ok) {
            throw new Error("City not found");
          }
          const data = await response.json();

          const closestForecast = data.list.find((item) => {
            const forecastDate = new Date(item.dt * 1000);
            return (
              forecastDate.getDate() === currentDate.getDate() &&
              forecastDate.getMonth() === currentDate.getMonth() &&
              forecastDate.getFullYear() === currentDate.getFullYear()
            );
          });

          if (!closestForecast) {
            weatherDiv.style.display = "block";
            weatherDiv.innerHTML = `
              <div class="date">${formatDate(currentDate)}</div>
              <p style="color: red;">No weather data available for this date.</p>
            `;
            return;
          }

          const { main, weather, wind } = closestForecast;
          weatherDiv.style.display = "block";
          weatherDiv.innerHTML = `
            <div class="date">${formatDate(currentDate)}</div>
            <h2>Weather in ${data.city.name}</h2>
            <p><strong>Temperature:</strong> ${main.temp}°C</p>
            <p><strong>Description:</strong> ${weather[0].description}</p>
            <p><strong>Humidity:</strong> ${main.humidity}%</p>
            <p><strong>Wind Speed:</strong> ${wind.speed} m/s</p>
          `;
        } catch (error) {
          weatherDiv.style.display = "block";
          weatherDiv.innerHTML = `<p style="color: red;">Error: ${error.message}</p>`;
        }
      }
    </script>
  </body>
</html>
