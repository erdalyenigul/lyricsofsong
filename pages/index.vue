<template>
  <div class="container">
    <div class="headline px-5 py-5">
      <div class="logo">
        <img src="@/assets/images/logo.jpg" alt="Lyrics of Song">
      </div>
    </div>
    <div class="px-6 is-flex flex-wrap has-shadow">
      <input class="input is-medium" type="text" placeholder="Search a song.." v-model="search" @keyup.enter="getSongDetail()">
      <button class="button is-medium is-dark" @click="getSongDetail()">Search</button>
    </div>

    <div class="section detail" v-if="charts">
      <h1 class="is-flex is-justify-content-center has-text-weight-bold is-size-5">{{ countryName }} Top 20</h1>
      <div class="cardWrap">
        <div class="cardCol" v-for="(song, index) in charts" :key="index">
          <div class="card"> 
            <div class="card-image">
              <figure class="image is-4by3">
                <img v-if="song.images" :src="song.images.coverart" :alt="song.subtitle">
                <img v-else src="https://www.wmhbradio.org/wp-content/uploads/2016/07/music-placeholder.png" alt="No Image">
              </figure>
            </div>
            <div class="card-content">
              <div class="media">
                <div class="media-content">
                  <p class="title is-5">{{ song.subtitle }}</p>
                  <p class="subtitle is-6">{{ song.title }}</p>
                  <a :href="song.url" target="_blank" class="button is-link">Get Details</a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="section">
      <h1 class="is-flex is-justify-content-center has-text-weight-bold is-size-5">World Lists</h1>
      <div class="cardWrap">
        <div class="cardCol" v-for="(item, index) in countries" :key="index">
          <a class="button is-link getListBtn" @click="getCountryDetail(item)">{{ item.name }}</a>
        </div>
      </div>
    </div>

    <div class="section detail">
      <div class="cardWrap">
        <div class="cardCol" v-for="(song, index) in results" :key="index">
          <div class="card"> 
            <div class="card-image">
              <figure class="image is-4by3">
                <img :src="song.track.images.coverart" :alt="song.track.subtitle">
              </figure>
            </div>
            <div class="card-content">
              <div class="media">
                <div class="media-content">
                  <p class="title is-5">{{ song.track.subtitle }}</p>
                  <p class="subtitle is-6">{{ song.track.title }}</p>
                  <a :href="song.track.url" target="_blank" class="button is-link">Get Details</a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
  </div>
</template>

<script>
export default {
  data() {
    return{
      search: '',
      results: null,
      countries: null,
      charts: null,
      countryName: ''
    }
  },
  methods: {
    scrollToTop() {
      window.scrollTo(0, 0);
    },
    getCountryDetail(item) {
      this.charts = null;
      this.countryName = item.name;
      this.$axios({
        url: 'https://shazam.p.rapidapi.com/charts/track',
        params: {locale: 'en-US', listId: item.listid, pageSize: '20', startFrom: '0'},
        headers: {
          'x-rapidapi-key': 'f13dba5445mshbdab50560069bafp100536jsn16ec8cb0e5f8',
          'x-rapidapi-host': 'shazam.p.rapidapi.com'
        }
      })
      .then((response) => {
        this.charts = response.data.tracks;
        this.scrollToTop();
      })
      .catch((error) => {
        console.log(error);
      });
    },
    getCountryList() {
      this.$axios({
        method: 'GET',
        url: 'https://shazam.p.rapidapi.com/charts/list',
        headers: {
          'x-rapidapi-key': 'f13dba5445mshbdab50560069bafp100536jsn16ec8cb0e5f8',
          'x-rapidapi-host': 'shazam.p.rapidapi.com'
        }
      })
      .then((response) => {
        this.countries = response.data.countries;

      })
      .catch((error) => {
        console.log(error);
      });
    },
    getSongDetail() {
      this.$axios({
        method: 'GET',
        url: 'https://shazam.p.rapidapi.com/search',
        params: {term: this.search, locale: 'en-US', offset: '0', limit: '3'},
        headers: {
          'x-rapidapi-key': 'f13dba5445mshbdab50560069bafp100536jsn16ec8cb0e5f8',
          'x-rapidapi-host': 'shazam.p.rapidapi.com'
        }
      })
      .then((response) => {
        this.results = response.data.tracks.hits;
      })
      .catch((error) => {
        console.log(error);
      });
    }
  },
  mounted() {
    this.getCountryList();
  }
}
</script>