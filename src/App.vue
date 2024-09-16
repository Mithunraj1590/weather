<template>
  <section class="relative w-full h-screen py-[100px]">
    <div class="absolute left-0 top-0 w-full h-full">
      <img class="w-full h-full object-cover blur-sm" src="/image/banner.jpg" alt="">
    </div>
    <div class="container relative z-10">
      <select v-model="selectedCity" id="location" class="w-full lg:w-[300px] bg-[#FFDBB5] mb-3 h-[40px] el-range-separator px-3 fw-medium rounded-xl">
        <option v-for="city in cities" :key="city.name" :value="city">{{ city.name }}</option>
      </select>

      <div class="grid grid-row-2 lg:grid-row-1 grid-cols-1 lg:grid-cols-3 gap-y-5 lg:gap-x-4">
        <div class="w-full lg:w-[300px] h-auto py-6 bg-[#FFDBB5] rounded-[30px] text-center relative text-black">
          <DateRange @date-selected="handleDateRange" />
          <div class="flex items-center justify-center text-[#FF885B]">
            <img v-if="weatherIconUrl" :src="weatherIconUrl" alt="Weather Icon" class="w-[50px] h-[50px]" />
            <h1 class="ttl text-[60px] font-bold">{{ weatherData?.main?.temp ? Math.round(weatherData.main.temp - 273.15) + '°C' : '--' }}</h1>
          </div>
          <p class="text-[#FF885B] text-[20px] font-semibold mb-2">{{ weatherDescription  }}</p>
          <!-- <p class="text-[#FF885B] text-[20px] font-semibold mb-2">{{ weatherData?.weather?.[0]?.main || 'N/A' }}</p> -->
          <p class="text-[#FF885B] mb-2 font-medium">{{ selectedCity?.name }}</p>
          <p class="text-[#FF885B] mb-2 font-medium">{{ formattedDate }}</p>
          <p class="text-[#FF885B] font-medium">Feel like {{ weatherData?.main?.feels_like ? Math.round(weatherData.main.feels_like - 273.15) + '°C' : '--' }}, Sunset {{ sunsetTime }}</p>
        </div>

        <div class="col-span-2">
          <TimeWeather :locationId="selectedCity.lat" :apiKey="API_KEY" />
          <div class="mt-3">
            <h3 class="ttl text-[25px] text-white">Random Text</h3>
            <p>Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, watch, computed } from 'vue'
import axios from 'axios'
import DateRange from './components/DateRange.vue'
import TimeWeather from "./components/TimeWeather.vue";

const API_KEY = import.meta.env.VITE_API_KEY

const cities = [
  { name: 'Delhi', lat: 1264527 },
  { name: 'Moscow', lat: 524901 },
  { name: 'Paris', lat: 2988507 },
  { name: 'New York', lat: 5128581 },
  { name: 'Sydney', lat: 2147714 },
  { name: 'Riyadh', lat: 108410 }
]

const selectedCity = ref(cities[0])
const selectedDateRange = ref(null)
const weatherData = ref(null)
const weatherIconUrl = ref(null)
const loading = ref(false)
const error = ref(null)

const fetchData = async () => {
  const { lat } = selectedCity.value
  if (!lat) return

  loading.value = true
  error.value = null
  try {
    const response = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?id=${lat}&appid=${API_KEY}`
    )
    weatherData.value = response.data

    const iconCode = response.data.weather[0].icon
    weatherIconUrl.value = `http://openweathermap.org/img/wn/${iconCode}@2x.png`
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

// Handle date range selection
const handleDateRange = (dates) => {
  selectedDateRange.value = dates
  fetchData()
}


watch(selectedCity, fetchData)


const formattedDate = computed(() => {
  const date = new Date()
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  })
})


const sunsetTime = computed(() => {
  if (!weatherData.value) return '--'
  const sunset = new Date(weatherData.value.sys.sunset * 1000)
  return sunset.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit', hour12: true })
})

const weatherDescription = computed(() => {
  if (!weatherData.value) return 'N/A'


  const currentHour = new Date().getHours()
  

  const timeOfDay = (currentHour >= 6 && currentHour < 18) ? 'day' : 'night'

  const mainWeather = weatherData.value.weather[0].main.toLowerCase()

  if (mainWeather.includes('rain')) {
    return 'Rainy'
  } else if (timeOfDay === 'day' && mainWeather.includes('clear')) {
    return 'Sunny'
  } else if (timeOfDay === 'night' && mainWeather.includes('clear')) {
    return 'Night'
  } else {
    return mainWeather.charAt(0).toUpperCase() + mainWeather.slice(1)
  }
})

</script>
