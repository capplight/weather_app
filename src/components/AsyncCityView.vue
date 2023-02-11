<template>
  <div class="flex flex-col flex-1 items-center">
    <!--Банер-->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" button to start
        tracking this city.
      </p>
    </div>
    <!--Общая погода-->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-4">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </p>
      <div class="flex items-center">
        <img
          class="w-[150px] h-auto mb-8"
          :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
          alt=""
        />
        <p class="text-6xl mb-8">
          {{ Math.round(weatherData.current.temp) }}&deg;C
        </p>
      </div>
      <div class="flex flex-row gap-1 mb-3">
        <p>
          Feels like {{ Math.round(weatherData.current.feels_like) }}&deg;C.
        </p>
        <p class="capitalize">
          {{ weatherData.current.weather[0].description }}
        </p>
      </div>
      <div class="flex flex-row gap-4 mb-3">
        <div class="flex flex-row gap-1 items-center">
          <i class="fa-solid fa-wind"></i>
          <p>{{ weatherData.current.wind_speed }}m/s SSE</p>
        </div>
        <div class="flex flex-row gap-1 items-center">
          <i class="fa-solid fa-gauge"></i>
          <p>{{ weatherData.current.pressure }}hPa</p>
        </div>
      </div>
      <div class="flex flex-row gap-4 mb-3">
        <p>
          Humidity:
          {{ weatherData.current.humidity }}%
        </p>
        <p>
          Dew pont:
          {{ weatherData.current.dew_point }}&deg;C
        </p>
      </div>
      <p>
        Visibility:
        {{ weatherData.current.visibility }}km
      </p>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />

    <!--Погода по часам-->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll scrollbar-none">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            />
            <p class="text-xl">{{ Math.round(hourData.temp) }}&deg;C</p>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!--Погода по неделям-->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max) }}&deg;C</p>
            <p>L: {{ Math.round(day.temp.min) }}&deg;C</p>
          </div>
        </div>
      </div>
    </div>

    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`
    );
    // подсчет настоящих даты и времени
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;
    // подсчет почасового смещения погоды
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });

    //Задержка для красоты
    await new Promise((res) => setTimeout(res, 800));

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();
const router = useRouter();

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};
</script>
