<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="mb-1 day">Today</h2>
          <p class="text-light date mb-0">{{ Date }}</p>
          <small>{{ time }}</small>
          <h2 class="place">
            {{ name }}<small>{{ country }}</small>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;</h1>
            <h2 class="text-light">{{description}}</h2>
          </div>
        </div>
      </div>
    </div>
    <div class="card card-2 w-100">
      <table class="m-4">
        <tbody>
          <tr>
            <th>Sea Level</th>
            <td>{{ sea_level }}</td>
          </tr>
          <tr>
            <th>Humidity</th>
            <td>{{ humidity }}</td>
          </tr>
          <tr>
            <th>Wind</th>
            <td>{{ wind }}</td>
          </tr>
        </tbody>
      </table>

      <DaysWeather :cityname="cityname"></DaysWeather>

      <div id="div_Form" class="d-flex m-3 justify-content-center">
        <form action="">
          <input
            type="button"
            value="Change Location"
            class="btn change-btn btn-primary"
          />
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import DaysWeather from "./DaysWeather.vue";

export default (await import("vue")).defineComponent({
  name: "myWeather",
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
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=3988d0b06a0c39602b5cec937020a31c`
    );
    const weatherData = response.data;
    this.temperature = Math.round(weatherData.main.temp);
    this.description = weatherData.weather[0].description;
    this.name = weatherData.name;
    this.wind = weatherData.wind.speed;
    this.sea_level = weatherData.main.sea_level;
    this.country = weatherData.sys.country;
    this.humidity = weatherData.main.humidity;
    this.iconUrl = `https://api.openweathermap.org/img/${weatherData.weather[0].icon}.png`;
    const d = new Date();
    this.time = d.getHours() + ":" + d.getMinutes() + ":" + d.getSeconds();
    this.date =
      d.getDate() + "-" + this.monthNames[d.getMonth] + "-" + d.getFullYear();
    console.log(this.iconUrl);
  },
});
</script>

<style>
body {
  background-color: aqua;
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
  background-image: url();
  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-repeat: no-repeat;
}
.temp {
  position: absolute;
  bottom: 0;
}
.main-div:hover {
  transform: scale(1.1);
  transition: transform 0.5s ease;
  z-index: 1;
}
.card-2 {
  background-color: aquamarine;
  border-radius: 25px;
}
h2,
p {
  padding: 0 2px 0;
}
.card-details {
  margin-left: 19px;
}
.h1_left {
  position: absolute;
  bottom: 30px;
  left: 16px;
  font-size: 4vw;
  line-height: 1.2;
}
.h3_left {
  position: absolute;
  left: 16px;
  font-size: 4vw;
  line-height: 0.5;
}
.h3_left small {
  font-size: 1rem;
}
table {
  position: relative;
  left: 15px;
  border-collapse: separate;
  border-spacing: 15px;
  width: 85px;
  margin: 0 auto;
  max-width: 600px;
}

th,
td {
  font-size: 18px;
  color: blue;
}
table,
tr:hover {
  color: antiquewhite;
}
td {
  text-align: right;
}
.change_btn {
  background-image: linear-gradient(to right, cyan, magenta);
}
.change_btn:hover {
  transform: scale(0, 9);
}
h2.text-light {
  font-size: 20px;
  color: black;
  font-weight: 400;
}
</style>
