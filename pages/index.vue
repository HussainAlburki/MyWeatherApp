<template>
  <div class="container">
    <h1>Weather App</h1>
    <div class="location">
      <input type="text" v-model="city" placeholder="Enter a city">
      <button @click="getWeather">Get Weather</button>
    </div>
    <div class="weather" v-if="weather">
      <div class="current">
        <img :src="`http://openweathermap.org/img/w/${weather.weather[0].icon}.png`" alt="weather icon">
        <p>{{ weather.weather[0].description }}</p>
        <p>{{ Math.round(weather.main.temp) }}°C</p>
      </div>
      <div class="details">
        <p>Humidity: {{ weather.main.humidity }}%</p>
        <p>Wind Speed: {{ weather.wind.speed }} km/h</p>
        <p>Feels Like: {{ Math.round(weather.main.feels_like) }}°C</p>
        <p>Pressure: {{ weather.main.pressure }} hPa</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weather: null,
    };
  },
  methods: {
    async getWeather() {
      try {
        const apiKey = 'd66a27e2f474ccb62a09af3b637852ba';
        const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${apiKey}&units=metric`;
        const response = await fetch(url);
        const data = await response.json();
        this.weather = data;
      } catch (error) {
        console.error(error);
      }
    },
  },
};
</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  text-align: center;
}

.location {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

.location input[type="text"] {
  padding: 10px;
  border-radius: 4px;
  border: 1px solid #ccc;
  margin-right: 10px;
  font-size: 16px;
}

.location button {
  padding: 10px 20px;
  border-radius: 4px;
  border: none;
  background-color: #007bff;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
}

.weather {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

.current img {
  width: 100px;
  height: 100px;
}

.current p {
  font-size: 24px;
  font-weight: bold;
  margin: 0;
}

.details {
  display: flex;
  flex-direction: column;
  margin-top: 20px;
}

.details p {
  font-size: 18px;
  margin: 5px 0;
}
</style>