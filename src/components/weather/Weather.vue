<template src="./weather.html"></template>

<script>
import Vue from 'vue'
import Vuex from 'vuex'
import axios from 'axios'
import sunny from '../../assets/sunny.png'
import partlySunny from '../../assets/partlySunny.png'
import mostlySunny from '../../assets/mostlySunny.png'
import cloudy from '../../assets/cloudy.png'
import partlyCloudy from '../../assets/partlyCloudy.png'
import mostlyCloudy from '../../assets/mostlyCloudy.png'
import scatteredShowers from '../../assets/scatteredShowers.png'
import thunderstorms from '../../assets/thunderstorms.png'
import scatteredThunderstorms from '../../assets/scatteredThunderstorms.png'
import rain from '../../assets/rain.png'
import breezy from '../../assets/breezy.png'

Vue.use(Vuex)

export default {
  name: 'weather',
  data () {
    return {
      city: '',
      forecast: [],
      showError: false,
      showForecast: false
    }
  },
  methods: {
    search: function () {
      this.showError = false
      var url = 'https://query.yahooapis.com/v1/public/yql?format=json&env=store%3A%2F%2Fdatatables.org%2Falltableswithkeys&q='
      var query = 'select * from weather.forecast where woeid in (select woeid from geo.places(1) where text="' + this.city + '")'
      axios.get(url + query).then(response => {
        var days = response.data.query.results.channel.item.forecast
        this.forecast = days.slice(0, 5).map(this.buildForecastElement)
        this.showForecast = true
      })
      .catch(e => {
        this.showError = true
      })
    },
    loadImage: function (description) {
      var images = {
        'Sunny': sunny,
        'Partly Sunny': partlySunny,
        'Mostly Sunny': mostlySunny,
        'Cloudy': cloudy,
        'Partly Cloudy': partlyCloudy,
        'Mostly Cloudy': mostlyCloudy,
        'Scattered Showers': scatteredShowers,
        'Thunderstorms': thunderstorms,
        'Scattered Thunderstorms': scatteredThunderstorms,
        'Rain': rain,
        'Breezy': breezy
      }

      return images[description]
    },
    dayOfTheWeek: function (index) {
      return ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'][index]
    },
    buildForecastElement: function (forecast) {
      return {
        day: this.dayOfTheWeek((new Date(forecast.date)).getDay()),
        temperature: {
          high: forecast.high,
          low: forecast.low
        },
        image: this.loadImage(forecast.text)
      }
    }
  }
}
</script>

<style src='./weather.css'></style>
