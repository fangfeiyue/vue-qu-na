<template>
    <div>
      <city-header></city-header>
      <city-search :cities="cities"></city-search>
      <city-list :letterIndex="letterIndex" :letter = 'letter' :cities="cities" :hotCities="hotCities"></city-list>
      <city-alphabet :cities="cities" @handleLetterClick="handleLetterClick"></city-alphabet>
    </div>
</template>

<script>
import axios from 'axios'
import CityHeader from './components/Header'
import CitySearch from './components/Search'
import CityList from './components/List'
import CityAlphabet from './components/Alphabet'
export default {
  name: 'City',
  components: {
    CityHeader,
    CitySearch,
    CityList,
    CityAlphabet
  },
  data () {
    return {
      cities: {},
      hotCities: [],
      letter: '',
      letterIndex: 0
    }
  },
  methods: {
    getCityInfo () {
      axios.get('/api/city.json').then(this.handleGetCityInfoSucc)
    },
    handleGetCityInfoSucc (res) {
      res = res.data
      if (res.ret && res.data) {
        const data = res.data
        this.cities = data.cities
        this.hotCities = data.hotCities
      }
      console.log(res)
    },
    handleLetterClick (letter) {
      this.letter = letter
    }
  },
  mounted () {
    this.getCityInfo()
  }

}
</script>

<style lang="stylus" scoped>

</style>
