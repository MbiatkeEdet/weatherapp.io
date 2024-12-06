<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="mb-1 day">Today</h2>
          <p class="text-bold date mb-0">{{ date }}</p>
          <small>{{ time }}</small>
          <h2 class="place">
            {{ name }}<small>, {{ country }}</small>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;C</h1>
            <h2 class="text-bold">{{ description }}</h2>
            <img v-if="iconUrl" :src="iconUrl" alt="Weather Icon" />
          </div>
        </div>
      </div>
    </div>
    <div class="card card-2 w-100">
      <table class="m-4">
        <tbody>
          <tr>
            <th>Sea Level</th>
            <td>{{ sea_level ? sea_level + " m" : "N/A" }}</td>
          </tr>
          <tr>
            <th>Humidity</th>
            <td>{{ humidity }}%</td>
          </tr>
          <tr>
            <th>Wind</th>
            <td>{{ wind }} m/s</td>
          </tr>
        </tbody>
      </table>

      <DaysWeather :cityname="cityname" />

      <div id="div_Form" class="d-flex m-3 justify-content-center">
        <form>
          <input
            type="button"
            value="Change Location"
            class="btn btn-primary change-btn"
          />
        </form>
      </div>
    </div>
  </div>
</template>


<script>
import axios from "axios";
import DaysWeather from "./DaysWeather.vue";

export default {
  name: "MyWeather",
  components: {
    DaysWeather,
  },
  props: {
    city: String,
  },
  data() {
    return {
      cityname: this.city,
      temperature: null,
      description: null,
      iconUrl: null,
      date: null,
      time: null,
      name: null,
      sea_level: null,
      wind: null,
      country: null,
      humidity: null,
      monthNames: [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ],
    };
  },

  async created() {
    try {
      const response = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=3988d0b06a0c39602b5cec937020a31c`
      );
      const weatherData = response.data;
      this.temperature = Math.round(weatherData.main.temp);
      this.description = weatherData.weather[0].description;
      this.name = weatherData.name;
      this.wind = weatherData.wind.speed;
      this.sea_level = weatherData.main.sea_level || null; // Handle unavailable sea level
      this.country = weatherData.sys.country;
      this.humidity = weatherData.main.humidity;
      this.iconUrl = `https://openweathermap.org/img/wn/${weatherData.weather[0].icon}.png`;
      
      const now = new Date();
      this.time = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
      this.date = `${now.getDate()} ${this.monthNames[now.getMonth()]} ${now.getFullYear()}`;
    } catch (error) {
      console.error("Error fetching weather data:", error);
    }
  },
};
</script>


<style>
body {
  background-color: none;
}
.weather-temp {
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}
h2.mb-1.day {
  font-size: 3rem;
  font-weight: 400;
}
.main-div {
  border-radius: 25px;
  color: #fff;
  background-color: #1e2a38;
  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-repeat: no-repeat;
}
.temp {
  position: relative;
  bottom: 10px;
}
.main-div:hover {
  transform: scale(1.05);
  transition: transform 0.5s ease;
}
.card-2 {
  background-color: aquamarine;
  border-radius: 25px;
  padding: 1rem;
}
h2,
p {
  margin: 0 5px;
}
table {
  margin: 0 auto;
  width: 100%;
  max-width: 600px;
  border-spacing: 10px;
}
th,
td {
  font-size: 1rem;
  text-align: left;
  padding: 5px;
  color: #333;
}
tr:hover td {
  color: #007bff;
}
.change-btn {
  background-image: linear-gradient(to right, cyan, magenta);
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 5px;
  cursor: pointer;
}
.change-btn:hover {
  background-image: linear-gradient(to right, magenta, cyan);
  transform: scale(1.05);
  transition: transform 0.3s ease;
}
h2.text-light {
  font-size: 1.25rem;
  color: #666;
  font-weight: 400;
}
</style>
