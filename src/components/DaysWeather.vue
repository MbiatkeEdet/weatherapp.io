<template>
  <div class="days-tab text-center">
    <div v-if="loading" class="loading">Loading...</div>
    <ul v-else class="p-0">
      <li v-for="day in forecast" :key="day.date" class="li_active">
        <div class="py-3">
          <img :src="day.iconUrl" alt="Weather Icon" class="icon" />
        </div>
        <div class="py-3">{{ day.date }}</div>
        <div class="py-3">{{ day.temperature }}&deg;C</div>
        <div class="py-3 text-gray-300">{{ day.description }}</div>
      </li>
    </ul>
  </div>
</template>

  
  <script>
import moment from "moment";
import axios from "axios";

export default {
  props: {
    cityname: String,
  },
  data() {
    return {
      forecast: [],
      loading: true,
    };
  },
  mounted() {
    this.fetchWeatherData();
  },
  methods: {
    async fetchWeatherData() {
      const apiKey = "3988d0b06a0c39602b5cec937020a31c";
      const city = this.cityname;
      const apiUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;

      try {
        const response = await axios.get(apiUrl);
        const forecastData = response.data.list;

        const filteredData = forecastData
          .map((item) => ({
            date: moment(item.dt_txt).format("dddd, MMM D"),
            temperature: Math.round(item.main.temp),
            description: item.weather[0].description,
            iconUrl: `https://openweathermap.org/img/wn/${item.weather[0].icon}.png`,
          }))
          .reduce((acc, item) => {
            if (!acc.some((day) => moment(day.date).isSame(item.date, "day"))) {
              acc.push(item);
            }
            return acc;
          }, [])
          .slice(0, 5); // Show 5 days forecast

        this.forecast = filteredData;
        this.loading = false;
      } catch (error) {
        console.error("Error fetching weather data:", error);
        this.loading = false;
      }
    },
  },
};
</script>

  
  <style>
.days-tab {
  width: 90%;
  box-shadow: 0 4px 8px black;
  border-radius: 20px;
  margin: auto;
  background: #1e2a38;
  padding: 1rem;
}
.loading {
  color: aquamarine;
  font-size: 1.5rem;
}
ul {
  margin: 0;
}
li {
  display: inline-block;
  list-style: none;
  height: 100%;
  width: 21%;
  max-width: 21%;
  font-size: 1rem;
  line-height: 1.2;
}
.icon {
  width: 40px;
  height: 40px;
}
.li_active {
  background: #253d5c;
  color: azure;
  border-radius: 10px;
  margin: 0.5rem;
  font-weight: 600;
  text-align: center;
}
.li_active:hover {
  transform: scale(1.1);
  transition: transform 0.3s ease;
}
</style>

  