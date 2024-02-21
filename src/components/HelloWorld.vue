<script setup>
import { ref, reactive, onMounted } from "vue";
import { useAxios } from "@vue-composable/axios";
import { watch } from "@vue/runtime-core";

defineProps({
  msg: String,
});

const cardLoading = ref(false);
const current = reactive({
  temp_c: 0,
  city: "Hanoi",
  country: "Vietnam",
});

const getWeather = (loca = "Hanoi") => {
  return useAxios({
    url: `http://api.weatherapi.com/v1/forecast.json?key=61667d987a214e68a0f53348222502&q=${loca}&days=3&aqi=no&alerts=no`,
    method: "GET",
    headers: null,
  });
};

const weatherForecast = () => {
  const { loading, status, data } = getWeather();
  cardLoading.value = true;
  watch(loading, () => {
    if (status.value === 200) {
      current.temp_c = data.value.current.temp_c;
      cardLoading.value = false;
    } else return;
  });
};

onMounted(() => {
  weatherForecast();
});
</script>

<template>
  <div class="flex-1 flex items-center justify-center">
    <div
      class="bg-gradient-to-tr from-zinc-950 to-zinc-100 border rounded border-1 p-4 w-[300px]"
    >
      <div class="flex justify-between">
        <span>
          {{ current.city }}
        </span>
        <span> 14:00 </span>
      </div>
      <div class="grid justify-items-center">
        <img :src="'//cdn.weatherapi.com/weather/64x64/day/119.png'" alt="" />
        <span>Cloudy</span>
      </div>
      <div class="flex justify-between mt-6">
        <span class="text-white">
          {{ current.city }}
        </span>
        <span class="text-2xl"> {{ current.temp_c }}°C </span>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
