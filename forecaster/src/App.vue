<template>
  <div class="page">
    <main class="main">
      <div class="container">
        <div class="laptop">
          <div class="sections">
            <section :class="['section', 'section-left', {'section-error': isError}]">
              <div class="info">
                <div class="city-inner">
                  <input 
                    v-model="city"
                    @keyup.enter="getWeather" 
                    type="text" 
                    class="search" />
                    <button 
                      @click="getWeather" 
                      class="search-btn"></button>
                </div>
                <WeatherSummary 
                  v-if="!isError" 
                  :weatherInfo="weatherInfo"/>
                  <div v-else class="error">
                    <div class="error-title">Oooops! something went wrong</div>
                    <div 
                      v-if="weatherInfo?.message" 
                      class="error-message">
                      {{ capitalizeFirstLetter(weatherInfo?.message) }}
                  </div>
                  </div>
              </div>
            </section>
            <section 
              v-if="!isError" 
              class="section section-right">
              <div class="section highlights">
                <div class="title">Today's Highlights</div>
                <Highlights :weatherInfo="weatherInfo"/>
              </div>
            </section>
          </div>
          <div 
            v-if="!isError" 
            class="sections">
            <Coords :coord="weatherInfo.coord"/>
            <Humidity :humidity="weatherInfo.main.humidity"/>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, computed} from 'vue'
import WeatherSummary from './components/WeatherSummary.vue';
import Highlights from './components/HighLights.vue';
import Coords from './components/Coords.vue';
import Humidity from './components/Humidity.vue';
import { API_KEY, BASE_URL } from './constants';
import { capitalizeFirstLetter } from './utils'

const city = ref('Moscow')
const weatherInfo = ref(null)
const isError = computed(() => weatherInfo.value?.cod !== 200)

function getWeather() {
  fetch(`${BASE_URL}?q=${city.value}&units=metric&appid=${API_KEY}`)
   .then((response) => response.json())
   .then((data) => weatherInfo.value = data)
}

onMounted(getWeather)
</script>

<style scoped lang="scss">
@import "./assets/styles/main.scss";

.page {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px 0;
  background-color: #59585d;
}
.laptop {
  width: 100%;
  padding: 20px;
  background-color: #0e100f;
  border-radius: 25px;
}
.sections {
  display: flex;
  width: 100%;
}
.section-left {
  width: 30%;
  padding-right: 10px;

  &.section-error {
    min-width: 100%;
    width: auto;
    padding-right: 0;
  }
}

.error {
  padding-top: 20px;

  &-title {
    font-size: 18px;
    font-weight: 700;
  }

  &-message {
    padding-top: 10px;
  
  }
}



.section-right {
  width: 70%;
  padding-left: 10px;
}
.city-inner {
  position: relative;
  display: inline-block;
  width: 100%;

  .search-btn {
    position: absolute;
    top: 0;
    right: 10px;
    width: 25px;
    height: 25px;
    background: url("./assets/img/search.svg") no-repeat 50% 50%;
    background-size: contain;
    transform: translateY(50%);
    cursor: pointer;
    border-width: 0;
    outline: none;
  }
}
.info {
  height: 100%;
  padding: 16px;
  background: url("./assets/img/gradient-1.jpg") no-repeat 50% 50%;
  background-size: cover;
  border-radius: 25px;
}
.search {
  width: 100%;
  padding: 16px;
  font-family: "Inter", Arial, sans-serif;
  color: $white;
  background-color: rgba(0, 0, 0, 0.75);
  border-radius: 16px;
  border: none;
  outline: none;
  cursor: pointer;
}
.section-bottom {
  width: 50%;
  margin-top: 16px;
}
</style>
