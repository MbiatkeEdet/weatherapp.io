<template>
   <div class="days-tab text-center">
    <div v-if="loading" class="loading">Loading...</div>
    <ul v-else class="p-0">
        <li v-for="day in forecast" :key="day.date" class="li_active">
        <div class="py-3">icon</div>
        <div class="py-3">{{ day.date }}</div>
        <div class="py-3">{{ day.temperature }}&deg;C</div>
        </li>
        <li class="li_active">
        <div class="py-3">icon</div>
        <div class="py-3">day</div>
        <div class="py-3">12oc</div>
        </li>
        <li class="li_active">
        <div class="py-3">icon</div>
        <div class="py-3">day</div>
        <div class="py-3">12oc</div>
        </li>
        <li class="li_active">
        <div class="py-3">icon</div>
        <div class="py-3">day</div>
        <div class="py-3">12oc</div>
        </li>
    </ul>
   </div>
  </template>
  
  <script>
   import moment from 'moment'
  import axios from 'axios';
  
  export default (await import('vue')).defineComponent( {
    props: {
      cityname: String
    },
    data (){
      return{
       forecast: [],
       loading: true,
       iconUrl: null,
      };
    },
    mounted() {
      this.fetchWeatherData();
    },
    methods: {
      async fetchWeatherData(){
        const apiKey = `3988d0b06a0c39602b5cec937020a31c`;
        const city = this.cityname;
        const apiUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;

        await axios.get(apiUrl).then(() => {
          const forecastData = Response.data.list;
          const filteredData = forecastData.map(item =>{
            return{
              data : moment(item.dt_txt.split('')[0]),
              temperature: Math.round(item.main.temp),
              description: item.weather[0].description,
              iconUrl: `https://api.openweathermap.org/img/w/${item.weather[0].icon}.png`
            };
          }).reduce((acc, item) =>{
            if(!acc.some(day => day.date.isSame(item.date, 'day'))){
              acc.push(item);
            }
            return acc;
          }, []).slice(1, 5);
          this.forecast = filteredData;
          this.loading = false;

        }).catch(error => {
          console.error('Error fetching weather data:', error);
          this.loading = false;
        });
      },
    }
    })
  </script>
  
  <style>
  .days-tab {
    width: 90%;
    box-shadow: o 4px 8px black;
    border-radius: 20px;
    width: 90%;
    margin: auto;
  }
  .loading{
    color: aquamarine;
  }
  ul{
    margin: 0;
  }
  li {
    display: inline-block;
    list-style: none;
    height: 100%;
    width: 21%;
    max-width: 21%;
    font-size: 1vw;
    line-height: 1.2;
  }

  span {
    display: block;
    margin-bottom: 5px;
    font: 100% sans-serif;
    height: 35px;
  }
  .li_active {
    background: #253d5c;
    color: azure;
    border-radius: 10px;
    margin: 0.5rem;
    color: aquamarine;
    font-weight: 600;
  }

  .li_active:hover {
    transform: scale(1.3);
    transition: transform 0.3s ease;
  }
  .li_active_temp {
    display: inline-block;
    background-color: aqua;
    transition: background-color 0.5s;
    border-radius: 15px;
  }
  .li_active_temp:hover{
    transform: scale(1.4);
    transition: transform 0.3s ease;
    background: #253d5c;
    border-radius: 10px;
    color: blue;
  }
  
  
  </style>
  