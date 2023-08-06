<template>
  <div class="weather-block">
    <div
        v-if="!settingsOpen"
        class="weather-block__icon"
        @click="settingsOpen = !settingsOpen"
    >
      <!--  icon by Icons8-->
      <img
          src="/icon/settings-32.svg"
          alt="icon"
      />
    </div>
    <SettingsForSelectedCities
        v-if="settingsOpen"
        class="weather-block__settings"
        :api-key="apiKey"
        @close="settingsOpen = false"
        @update-cities="updateCitiesData"
    />
    <div v-if="weathers.length === 0">You have not chosen a city</div>
    <div v-show="weathers.length">
      <div
          v-for="city in weathers"
          :key="city.name"
          class="weather-block__city"
      >
        <CityWeather
            :city="city"
        />
      </div>
    </div>
  </div>
</template>

<script>
import SettingsForSelectedCities from "@/components/SettingsForSelectedCities.vue";
import CityWeather from "@/components/CityWeather.vue";

export default {
  name: "WeatherWidget",
  props: {
    apiKey: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      settingsOpen: false,
      weathers: [],
    };
  },
  components: {CityWeather, SettingsForSelectedCities},
  mounted() {
    if (localStorage.getItem('cities')) {
      this.updateCitiesData(JSON.parse(localStorage.getItem('cities')))
    }
  },
  methods: {
    /**
     * @description  method for getting the coordinates of the selected city
     * @param {Array} cities - city name
     * @returns {object} - object with coordinates
     */
    updateCitiesData(cities) {
      const fetches = []
      cities.forEach((city) => {
        if (this.weathers.find((weather) => weather.name === city.name)) {
          return
        }
        fetches.push(this.getCityWeather(city.lat, city.lon, city.name, city.country))
      })

      Promise.all(fetches).then(() => {
        this.sortWeathers(cities)
      })

    },
    /**
     * @description method for sorting the weather array according to the order of cities in the settings
     * @param {Array} cities - city name
     */
    sortWeathers(cities) {
      const newWeathers = []
      cities.forEach((city) => {
        newWeathers.push(this.weathers.find((weather) => weather.name === city.name))
      })
      this.weathers = newWeathers
    },
    /**
     * @description method for getting weather data by coordinates
     * @param lat
     * @param lon
     * @param city
     * @param country
     * @returns {Promise<Response>}
     */
    async getCityWeather(lat, lon, city, country) {
      return fetch(`https://api.openweathermap.org/data/2.5/weather?units=Metric&lat=${lat}&lon=${lon}&appid=${this.apiKey}`)
          .then(response => response.json())
          .then(response => {
            this.weathers.push({
              name: city,
              country: country,
              temp: response.main.temp,
              icon: response.weather[0].icon,
              description: response.weather[0].description,
              wind: `${response.wind.speed} m/s ${this.convertDegreeToDirection(response.wind.deg)}`,
              wind_direction: response.wind.deg,
              hpa: response.main.pressure,
              humidity: response.main.humidity,
              dew_point: response.main.feels_like,
              visibility: response.visibility / 1000,
            })
          })
    },
    convertDegreeToDirection(degree) {
      if (degree > 337.5) return 'N'
      if (degree > 292.5) return 'NW'
      if (degree > 247.5) return 'W'
      if (degree > 202.5) return 'SW'
      if (degree > 157.5) return 'S'
      if (degree > 122.5) return 'SE'
      if (degree > 67.5) return 'E'
      if (degree > 22.5) return 'NE'
      return 'N'
    }
  },

}
</script>

<style scoped lang="scss">
.weather-block {
  position: fixed;
  padding: 20px 10px;
  width: 300px;
  height: 100vh;
  display: flex;
  flex-direction: column;
  right: 5px;
  top: 5px;
  overflow-y: scroll;
  border: 1px solid #ccc;
  border-radius: 4px;

  &__icon {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
    width: 20px;
    transition: all .3s ease;

    &:hover {
      transform: scale(1.1);
    }

    img {
      width: 100%;
    }
  }

  &__settings {
    position: absolute;
    top: 20px;
    right: 10px;
    z-index: 1;
  }

  &__city {
    margin-bottom: 20px;
  }
}

</style>