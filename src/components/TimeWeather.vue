<template>
    <div class="max-w-[600px] bg-[#FFE5CF] rounded-[30px] bg-clip-padding backdrop-filter backdrop-blur-sm bg-opacity-60 border-none p-3 lg:p-5">
      <ul class="w-full flex flex-wrap justify-between lg:gap-[30px] gap-y-[40px] lg:gap-y-[70px] relative bordered">
        <li v-for="(hour, index) in hourlyData" :key="index" class="w-[50px] lg:w-[80px] text-white text-[10px] lg:text-[16px]">
          <strong class="block text-center mb-2">{{ hour.time }}</strong>
          <div class="flex items-center justify-center">
            <img class="w-[20px] h-[20px]" v-if="hour.iconUrl" :src="hour.iconUrl" alt="weather icon" />
            <span> {{ hour.temp }}°</span>
          </div>
        </li>
      </ul>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onUnmounted, watch } from 'vue';
  import axios from 'axios';
  
  const props = defineProps({
    locationId: Number,
    apiKey: String
  });
  
  const hourlyData = ref([]);

  const fetchWeather = async () => {
    try {
      const response = await axios.get(
        `https://api.openweathermap.org/data/2.5/forecast?id=${props.locationId}&appid=${props.apiKey}&units=metric`
      );
  
      const now = new Date();
      const nowHour = now.getHours();
      const currentTime = now.getTime();
  
      let hoursArray = [];

      response.data.list.forEach(item => {
        const baseDate = new Date(item.dt * 1000);
        const baseHour = baseDate.getHours();
  
        for (let i = 0; i < 3; i++) {
          const hourDate = new Date(baseDate);
          hourDate.setHours(baseHour + i, 0, 0, 0);
  
          if (hourDate.getTime() >= currentTime) {
            hoursArray.push({
              time: baseHour + i === nowHour ? 'Now' : hourDate.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', hour12: true }),
              temp: item.main?.temp ?? 'N/A',
              iconUrl: item.weather?.[0]?.icon ? `https://openweathermap.org/img/wn/${item.weather[0].icon}.png` : null
            });
          }
        }
      });
  
      const sortedHoursArray = hoursArray
        .sort((a, b) => {
          const aDate = a.time === 'Now' ? now : new Date(`1970-01-01T${a.time}:00`);
          const bDate = b.time === 'Now' ? now : new Date(`1970-01-01T${b.time}:00`);
          return aDate - bDate;
        });
  
      const currentHourData = sortedHoursArray.find(hour => hour.time === 'Now');
      const otherHours = sortedHoursArray.filter(hour => hour.time !== 'Now');
  
      hourlyData.value = currentHourData ? [currentHourData, ...otherHours].slice(0, 10) : otherHours.slice(0, 10);
    } catch (err) {
      console.error("Error fetching hourly weather data:", err);
    }
  };
  

  const startHourlyUpdate = () => {
    fetchWeather();
  
    const intervalId = setInterval(() => {
      fetchWeather();
    }, 3600000); 
  
    onUnmounted(() => {
      clearInterval(intervalId);
    });
  };
  
  watch(() => props.locationId, fetchWeather, { immediate: true });
  
  onMounted(() => {
    startHourlyUpdate();
  });
  </script>
  
  <style scoped>
  .bordered {
    @apply after:absolute after:w-[95%] after:h-[1px] after:bg-white after:left-[50%] after:top-[50%] after:-translate-x-[50%];
  }
  </style>
  