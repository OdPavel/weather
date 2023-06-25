<script setup>
import {ref, onMounted, computed} from "vue";
import WeatherSummary from './components/WeatherSummary.vue'
import Highlights from "./components/Highlights.vue";
import Coords from "./components/Coords.vue"
import Humidity from "./components/Humidity.vue"
import {API_KEY, BASE_URL} from "./constans";
import {capitalizeFirstLetter} from "./utils";

const city = ref('Odessa')
const weatherInfo = ref(null)
const isError = computed(()=> weatherInfo?.value?.cod !==200)


function getWeather(){
  fetch(`${BASE_URL}?q=${city.value}&appid=${API_KEY}&units=metric`)
  .then((response)=>response.json())
  .then((data)=> weatherInfo.value = data)

}
onMounted(getWeather)
</script>

<template>
  <div class="page">
    <main class="main">
      <div class="container">
        <div class="laptop">
          <div class="sections">
            <section :class="['section','section-left', {'section-error': isError}]">
              <div class="info">
                <div class="city-inner">
                  <svg @click="getWeather" width="800px" height="800px" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <path d="M15 15L21 21" stroke="#ffffff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
                  <path d="M17 10C17 13.866 13.866 17 10 17C6.13401 17 3 13.866 3 10C3 6.13401 6.13401 3 10 3C13.866 3 17 6.13401 17 10Z" stroke="#ffffff" stroke-width="2"/>
                </svg>
                  <input
                      type="text"
                      class="search"
                      v-model="city"
                      @keyup.enter="getWeather"

                  >
                </div>

                  <WeatherSummary
                      v-if="!isError"
                      :weatherInfo="weatherInfo"
                  />

                <div v-else class="error">
                  <div class="error-title">Oooops! Something went wrong</div>
                  <div v-if="weatherInfo?.message" class="error-message">{{capitalizeFirstLetter(weatherInfo?.message)}}</div>
                </div>

              </div>
            </section>
            <section class="section section-right">

              <Highlights
                  v-if="!isError"
                  :weatherInfo="weatherInfo"
              />

            </section>
          </div>
          <div v-if="!isError" class="sections">
            <Coords :coord="weatherInfo.coord"/>
            <Humidity :humidity="weatherInfo.main.humidity" />
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style lang="sass" scoped>
@import "./assets/styles/main"
.page
  position: relative
  display: flex
  justify-content: center
  align-items: center
  min-height: 100vh
  padding: 20px 0
  background-color: #59585d

.laptop
  width: 100%
  padding: 20px
  background-color: #0e100f
  border-radius: 25px

.sections
  display: flex
  width: 100%

  @media (max-width: 767px)
    flex-direction: column

.section-left
  width: 30%
  padding-right: 10px

  @media (max-width: 767px)
    width: 100%
    padding-right: 0

  &.section-error
    min-width: 300px
    width: auto
    padding-right: 0

.section-right
  width: 70%
  padding-left: 10px

  @media (max-width: 767px)
    width: 100%
    margin-top: 16px
    padding-left: 0


.city-inner
  position: relative
  display: inline-block
  width: 100%

  svg
    content: ''
    position: absolute
    top: 0
    right: 10px
    width: 25px
    height: 25px
    //background: url('./assets/img/search.svg') no-repeat 50% 50%
    background-size: contain
    transform: translateY(50%)
    cursor: pointer

.info
  height: 100%
  padding: 16px
  background: url('./assets/img/gradient-1.jpg') no-repeat 50% 50%
  background-size: cover
  border-radius: 25px

.search
  width: 100%
  padding: 16px
  font-family: 'Inter', Arial, sans-serif
  color: $white
  background-color: rgba(0, 0, 0, 0.75)
  border-radius: 16px
  border: none
  outline: none
  cursor: pointer

.section-bottom
  width: 50%
  margin-top: 16px

  @media (max-width: 767px)
    width: 100%

.error
  padding-top: 20px

  &-title
    font-size: 18px
    font-weight: 700

  &-message
    padding-top: 10px
    font-size: 14px
</style>
