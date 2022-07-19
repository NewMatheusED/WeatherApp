<template>
  <div class="container">
    <h1 class="title">Weather App</h1>
    <div class="search" v-if="locate === ''">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Search">
        <button class="btn btn-primary" v-on:click="setLocate">Search</button>
      </div><!--form-group-->
    </div><!--search-->


    <div class="main" v-if="locate.length >= 1">
      <div class="row">
        <h2>{{results.location.name}}</h2> <img :src="results.current.condition.icon" alt="">
      </div><!--row-->
      <p>{{results.current.last_updated}}</p>
    </div><!--main-->
  </div><!--container-->
</template>

<script>
  export default {
    name: 'App',

    data() {
      return { 
        results: [],
        apiKey: '23c7a5af8a8e49968ee224129210102',
        locate: ''
      }
    },

    methods: {
      
      async getData() {
        try {
          const response = await fetch(`http://api.weatherapi.com/v1/current.json?key=${this.apiKey}&q=${this.locate}`);

          this.results = await response.json();

        } catch(error) {
          console.log(error)
        }
      },

      setLocate() {
        this.locate = document.querySelector('.form-control').value;
        this.getData();
      }
  },

  created() {
    this.getData();
  }
}
</script>

<style>
  .title {
    text-align: center;
    margin: 10px 0 30px;
  }

  .main .row {
    display: flex;
  }

  .main .row img {
    max-width: 100px;
    width: 100%;
    height: 100%;
  }
</style>