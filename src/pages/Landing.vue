<template>
  <div div id="landing" :class="background">
    <transition enter-active-class="animate__animated animate__flipInX" appear>
      <div id="card">
        <button
          v-is="'ion-icon'"
          name="refresh-outline"
          class="refresh"
          @click="updateInfo"
        ></button>
        <span v-is="'ion-icon'" :name="iconName" class="icon"></span>
        <p id="degrees">{{ temperature }}°C</p>
        <input id="city" v-model="city" :class="inputClass" />
      </div>
    </transition>
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
      city: '',
      iconName: '',
      background: '',
      inputClass: ''
    }
  },
  async created() {
    await this.getCity()
    await this.updateInfo()
  },
  mounted() {
    this.addPlugin('https://unpkg.com/ionicons@5.4.0/dist/ionicons/ionicons.js')
  },
  methods: {
    addPlugin(path: string) {
      const plugin = document.createElement('script')
      plugin.setAttribute('src', path)
      plugin.async = true
      document.head.appendChild(plugin)
    },
    async getCity() {
      try {
        const response = await axios.get(`https://ipinfo.io/json`)
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
        this.isCloudy = weather.cloud > 60 ? true : false
        this.isRaining = weather.condition.text.includes('rain')
        this.temperature = weather.temp_c
      } catch (error) {
        console.warn(error)
      }
    },
    getBackground() {
      if (this.isDay) {
        this.background = this.isCloudy ? 'day-cloudy' : 'day'
        this.inputClass = this.isCloudy ? 'dark' : 'light'
      } else {
        this.background = this.isCloudy ? 'night-cloudy' : 'night'
        this.inputClass = 'dark'
      }
    },
    getIcon() {
      if (this.isRaining) {
        this.iconName = 'thunderstorm-outline'
      } else if (this.isDay) {
        this.iconName = this.isCloudy ? 'cloud-outline' : 'sunny-outline'
      } else {
        this.iconName = this.isCloudy ? 'cloudy-night-outline' : 'moon-outline'
      }
    },
    async updateInfo() {
      await this.getWeather()
      await this.getIcon()
      await this.getBackground()
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
  color: #000;
}

.day-cloudy {
  background: linear-gradient(to right, #304352, #d7d2cc);
  color: #fff;
}

.night {
  background: linear-gradient(to right, #16222a, #3a6073);
  color: #fff;
}

.night-cloudy {
  background: linear-gradient(to right, #232526, #414345);
  color: #fff;
}

#card {
  z-index: 1;
  height: 40%;
  min-height: 350px;
  width: 15%;
  min-width: 250px;

  display: grid;
  grid-template-columns: auto;
  grid-template-rows: 30px 1fr 1fr 1fr;
  grid-gap: 0;

  justify-content: stretch;
  align-content: stretch;
  box-shadow: 0 0 1rem 0 rgba(0, 0, 0, 0.2);
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(5px);
}

.refresh,
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
  cursor: default;
}

#city {
  font-size: 1.5rem;
  font-family: 'Oswald', sans-serif;
  width: 70%;
  background: none;
  outline: none;
  text-align: center;
}

.refresh {
  font-size: 20px;
  left: 36%;
  cursor: pointer;
  transition: 0.5s;
  margin-top: 5px;
}

.refresh:hover {
  transform: rotate(180deg);
}

.dark {
  color: #fff;
  border: none;
}

.light {
  color: #000;
  border: none;
}

.dark:hover {
  border-bottom: solid 1px #fff;
}

.light:hover {
  border-bottom: solid 1px #000;
}
</style>
