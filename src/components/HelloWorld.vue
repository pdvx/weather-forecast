<script setup>
import { ref, reactive, onMounted } from "vue";
import { useAxios } from "@vue-composable/axios";
import { watch } from "@vue/runtime-core";
import moment from "moment";

defineProps({
  msg: String,
});

const weatherData = ref([
  {
    criteria: "UV INDEX",
    value: 0,
  },
  {
    criteria: "WIND",
    value: "0 km/h",
  },
  {
    criteria: "HUMIDITY",
    value: "0%",
  },
  {
    criteria: "VISIBILITY",
    value: "0 km",
  },
  {
    criteria: "PRESSURE",
    value: "0 hPa",
  },
  {
    criteria: "CLOUD COVER",
    value: "0%",
  },
]);

const cardLoading = ref(false);
const cityName = ref("");
const imgLink = ref("")
const current = reactive({
  temp_c: 0,
  city: "Hanoi",
  country: "Vietnam",
});
let currentTime = new Date();

const getWeather = (loca) => {
  return useAxios({
    url: `http://api.weatherapi.com/v1/current.json?key=61667d987a214e68a0f53348222502&q=${loca}&aqi=no`,
    method: "GET",
    headers: null,
  });
};

const weatherForecast = (loca = "Hanoi") => {
  const { loading, status, data } = getWeather(loca);
  cardLoading.value = true;
  watch(loading, () => {
    if (status.value === 200) {
      current.temp_c = data.value.current.temp_c;
      currentTime = moment(currentTime).format("HH:mm");
      weatherData.value = [
        {
          criteria: "UV INDEX",
          value: data.value.current.uv,
        },
        {
          criteria: "WIND",
          value: `${data.value.current.wind_kph} km/h`,
        },
        {
          criteria: "HUMIDITY",
          value: `${data.value.current.humidity}%`,
        },
        {
          criteria: "VISIBILITY",
          value: `${data.value.current.vis_km} km`,
        },
        {
          criteria: "PRESSURE",
          value: `${data.value.current.pressure_mb} hPa`,
        },
        {
          criteria: "CLOUD COVER",
          value: `${data.value.current.cloud}%`,
        },
      ];
      imgLink.value= data.value.current.condition.icon
      current.city = data.value.location.name;
    } else return;
  });
};

onMounted(() => {
  weatherForecast();
});
</script>

<template>
  <div class="flex-1 flex items-center justify-center">
    <div class="w-1/2">
      <div class="grid justify-items-center text-white">
        <span class="text-4xl font-semibold">
          {{ current.city }}
        </span>
        <a-input-group class="!flex justify-center mt-2" compact>
          <a-input
            class="w-1/4"
            placeholder="Enter a city name"
            v-model:value="cityName"
            allowClear
            @pressEnter="weatherForecast(cityName), (cityName = null)"
          />
          <button hidden ref="buttonSearch" @click="weatherForecast(cityName)">
            <img src="../assets/icon-search-input.svg" alt="" />
          </button>
          <a-button class="button-search" @click="() => buttonSearch.click()">
            <img src="../assets/icon-search-input.svg" alt="" />
          </a-button>
        </a-input-group>
        <img
          class="w-32 h-32 my-5"
          :src="imgLink"
          alt="Current weather img"
        />
        <span class="text-4xl font-semibold">{{ current.temp_c }}°</span>
      </div>
      <div
        class="mt-14 font-semibold grid grid-cols-3 gap-4 justify-items-center"
      >
        <div
          class="rounded-2xl bg-[#202B3B] w-40 h-20 p-5 opacity-100 grid"
          v-for="item in weatherData"
        >
          <span class="text-xs text-[#9399a2ff]">{{ item.criteria }}</span
          ><span class="text-2xl text-[#dde0e4ff]">
            {{ item.value }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
:deep() .ant-btn.button-search {
  border-top-left-radius: 0 !important;
  border-bottom-left-radius: 0 !important;
  background: #1677ff !important;
  border: unset !important;
}
</style>
