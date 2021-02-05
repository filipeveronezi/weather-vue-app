<template>
  <div div id="landing" class="day">
    <div id="card">
      <ion-icon name="sunny-outline" class="icon"></ion-icon>
      <p id="degrees">{{ temperature }}°C</p>
      <p id="city">{{ city }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from 'axios'
import WeatherAPI from '@/utils/WeatherAPI'

export default defineComponent({
  data() {
    return {
      isDay: false,
      isRaining: false,
      isCloudy: false,
      temperature: 0,
      ip: '',
      city: ''
    }
  },
  async created() {
    await this.getIp()
    await this.getCity()
    await this.getWeather()
  },
  methods: {
    async getIp() {
      try {
        const response = await axios.get('https://api.ipify.org?format=json')
        this.ip = response.data.ip.toString()
      } catch (error) {
        console.warn(error)
      }
    },
    async getCity() {
      try {
        const response = await axios.get(`http://ip-api.com/json/${this.ip}`)
        this.city = response.data.city.toString()
      } catch (error) {
        console.warn(error)
      }
    },
    async getWeather() {
      try {
        const response = await axios.get(
          `https://api.weatherapi.com/v1/current.json?key=${WeatherAPI.key}&q=${this.city}`
        )
        const weather = response.data.current
        this.isDay = weather.is_day == 1 ? true : false
        this.isCloudy = weather.cloud > 50 ? true : false
        this.isRaining = weather.condition.text.includes('rain')
        this.temperature = weather.temp_c
      } catch (error) {
        console.warn(error)
      }
    }
  }
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300;400;700&display=swap');

#landing {
  height: 100%;
  max-height: 100vh;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Oswald', sans-serif;
}

.day {
  background: linear-gradient(to right, #ffefba, #ffffff);
}

.day-cloudy {
  background: linear-gradient(to right, #304352, #d7d2cc);
}

.night {
  background: linear-gradient(to right, #16222a, #3a6073);
}

.night-cloudy {
  background: linear-gradient(to right, #232526, #414345);
}

#card {
  z-index: 1;
  height: 40%;
  min-height: 350px;
  width: 15%;
  min-width: 250px;
  display: grid;
  grid-template-columns: auto;
  grid-template-rows: 1fr 1fr 1fr;
  grid-gap: 0;
  justify-content: stretch;
  align-content: stretch;
  box-shadow: 0 0 1rem 0 rgba(0, 0, 0, 0.2);
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(5px);
}

.icon,
#degrees,
#city {
  justify-self: center;
  align-self: center;
}
.icon {
  font-size: 3.5rem;
}

#degrees {
  font-size: 2.4rem;
}

#city {
  font-size: 1.5rem;
}

img {
  position: absolute;
  width: 100%;
  top: -55%;
  z-index: 0;
}
</style>
