<template>
  <div class="container">
    <h1>Weather App</h1>
    <div class="location">
      <input type="text" v-model="city" placeholder="Enter a city" @keyup.enter="getWeather" @input="autocomplete">
      <div class="autocomplete" v-if="showAutocomplete">
        <ul>
          <li v-for="(location, index) in locations" :key="index" @click="selectLocation(location)">
            <span>{{ location.name }}, {{ location.country }}</span>
            <span class="coordinates">({{ location.lat }}, {{ location.lon }})</span>
          </li>
        </ul>
      </div>
      <button @click="getWeather">Get Weather</button>
    </div>
    <div class="weather" v-if="weather">
      
      <div class="current">
        <img :src="`http://openweathermap.org/img/w/${weather.weather[0].icon}.png`" alt="weather icon">
        <p>{{ weather.weather[0].description }}</p>
        <p>{{ Math.round(weather.main.temp) }}°C</p>
      </div>
      <div class="details">
  <h2>{{ weather.name }}</h2>
  <p>Current temperature: {{ Math.round(weather.main.temp) }}°C</p>
  <p>Humidity: {{ weather.main.humidity }}%</p>
  <p>Wind Speed: {{ weather.wind.speed }} km/h</p>
  <p>Feels Like: {{ Math.round(weather.main.feels_like) }}°C</p>
  <p>Pressure: {{ weather.main.pressure }} hPa</p>
</div>
    </div>
    <div v-else>
      <p>{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weather: null,
      errorMessage: '',
      showAutocomplete: false,
      locations: [],
      activeLocation: null,
      units: 'metric',
      input: null,
    };
  },
  methods: {
    async getWeather() {
      try {
        // Set the API key and endpoint URL
        const apiKey = '679989365c903ec1a05ff151cc919665';
        const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${apiKey}&units=${this.units}`;

        // Fetch the data from the API
        const response = await fetch(url);
        const data = await response.json();

        // Set the weather data and clear the error message if there was one
        this.weather = data;
        this.errorMessage = '';

        // Clear the city input and hide the autocomplete list
        this.city = '';
        this.showAutocomplete = false;
      } catch (error) {
        console.error(error);

        // Set the error message and clear the weather data
        this.errorMessage = 'An error occurred. Please try again.';
        this.weather = null;
      }
    },

    async autocomplete() {
      try {
        // Only call the autocomplete API if the city input has more than 2 characters
        if (this.city.length > 2) {
          // Set the API key and endpoint URL for the autocomplete API
          const apiKey = '679989365c903ec1a05ff151cc919665';
          const url = `https://api.openweathermap.org/geo/1.0/direct?q=${this.city}&limit=5&appid=${apiKey}`;

          // Fetch the data from the API
          const response = await fetch(url);
          const data = await response.json();

          // Set the locations array to the name and country code of each result
          this.locations = data.map((location) => {
            return {
              name: location.name,
              country: location.country
            }
          });
        } else {
          // If the city input has less than 3 characters, clear the locations array and hide the autocomplete list
          this.locations = [];
          this.showAutocomplete = false;
        }
      } catch (error) {
        console.error(error);

        // Set the error message and clear the locations array
        this.errorMessage = 'An error occurred while getting autocomplete results.';
        this.locations = [];
      }
    },

    // Add a new method to select a location from the autocomplete list
    selectLocation(location) {
      this.city = location.name;
      this.activeLocation = location;
      this.locations = [];
      this.showAutocomplete = false;
    }
  }
}
</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  text-align: center;
  padding-top: 50px;
}

h1 {
  font-size: 36px;
  margin-bottom: 30px;
}

.location {
  display: flex;
  flex-direction: column;
  margin-bottom: 30px;
}

.autocomplete {
  background-color: #fff;
  position: absolute;
  z-index: 1;
  top: 55px;
  left: 0;
  right: 0;
  border: 1px solid #ccc;
  max-height: 200px;
  overflow-y: scroll;
  box-shadow: 0px 2px 5px rgba(0,0,0,0.1);
}

.autocomplete ul {
  list-style: none;
  padding-left: 0;
  margin-top: 0;
  margin-bottom: 0;
}

.autocomplete li {
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.autocomplete li:hover {
  background-color: #f2f2f2;
}

.weather {
  margin-top: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0px 2px 5px rgba(0,0,0,0.1);
  padding: 20px;
}

.current {
  margin-right: 50px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.current img {
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}

.details {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-size: 16px;
}

.details h2 {
  margin: 20px 0 10px;
  font-size: 24px;
  font-weight: bold;
  text-transform: capitalize;
}

.details p {
  margin: 5px 0;
}


</style>