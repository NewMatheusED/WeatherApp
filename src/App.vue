<template>
  <div class="container">
    <h1 class="title">Weather App</h1>
    <div class="search" v-if="locate === ''">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Search">
        <button style="width: 100px; margin-top: 10px" class="btn btn-primary" v-on:click="setLocate">Search</button>

        <hr />

        <div v-bind:class = "(historico.length == 0) ? 'hide' : 'show'" class="main">
          <h2>Histórico</h2>
          <ul>
            <li v-for="(locate, index) in historico">
              <p>{{ locate }}</p>
              <button class="btn btn-primary" v-on:click="setLocateHistorico(locate)">Set local</button>
              <button style="margin-left: 10px" class="btn btn-danger" v-on:click="removeLocate(index)">Remover</button>
            </li>
          </ul>
        </div>
      </div><!--form-group-->
    </div><!--search-->

    <div class="main" v-if="locate.length >= 1">
      <div class="row">
        <h2>{{results.location.name}} ({{results.location.region}}, {{results.location.country}})</h2> <img :src="results.current.condition.icon" alt="">
      </div><!--row-->
      <p>Última atualização: {{results.current.last_updated}}</p>
      <hr />
      <div class="mainRow">
        <div class="col-md-6">
          <h3>Temperatura</h3>
          <p v-bind:class = "(results.current.temp_c <= 20) ? 'cold' : 'hot'" >{{results.current.temp_c}}°C</p>
          <p v-bind:class = "(results.current.temp_f <= 68) ? 'cold' : 'hot'">{{results.current.temp_f}}°F</p>
          <h5>Sensação térmica:</h5>
          <p v-bind:class = "(results.current.feelslike_c <= 20) ? 'cold' : 'hot'" >{{results.current.feelslike_c}}°C</p>
          <p v-bind:class = "(results.current.feelslike_f <= 68) ? 'cold' : 'hot'">{{results.current.feelslike_f}}°F</p>
        </div><!--col-md-6-->

        <hr />

        <div class="col-md-6">
          <h3>Condição</h3>
          <p>{{results.current.condition.text}}</p>
          <p>{{results.current.condition.date}}</p>
        </div><!--col-md-6-->

        <hr />

        <div class="col-md-6">
          <h3>Precipitação</h3>
          <p>{{results.current.precip_mm}} mm</p>
        </div><!--col-md-6-->

        <hr />

        <div class="col-md-6">
          <h3>Previsão</h3>
          <div class="row">
            <p>Máxima de: {{results.forecast.forecastday[0].day.maxtemp_c}} °C</p>
            <p>Máxima de: {{results.forecast.forecastday[0].day.maxtemp_f}} °F</p>
          </div><!--row-->

          <div class="row">
            <p>Mínima de: {{results.forecast.forecastday[0].day.mintemp_c}} °C</p>
            <p>Mínima de: {{results.forecast.forecastday[0].day.mintemp_f}} °F</p>
          </div><!--row-->

          <br />

          <div class="row">
            <p>Chance de chover: {{results.forecast.forecastday[0].day.daily_chance_of_rain}}%</p>
            <p>Chance de nevar: {{results.forecast.forecastday[0].day.daily_chance_of_snow}}%</p>
          </div><!--row-->
        </div><!--col-md-6-->

          <hr />

          <div style="max-width: 1000px; width: 100%; margin: 0 auto; text-align: center" class="col-md-6">
            <h3>Previsão para as próximas horas</h3>
            <h5>Do dia {{((results.forecast.forecastday[0].hour[0].time).split(' ')[0]).split('-')[2]}}/{{((results.forecast.forecastday[0].hour[0].time).split(' ')[0]).split('-')[1]}}</h5>
            <div class="rowFlex">
              <div class="item" v-for="n in 23">
                <p>{{(results.forecast.forecastday[0].hour[n].time).split(' ')[1]}}</p>
                <img :src="results.forecast.forecastday[0].hour[n].condition.icon" alt="">
                <span>{{(results.forecast.forecastday[0].hour[n].condition.text)}}</span>
                <!-- <br /> -->
                <span style="font-weight: bold">{{results.forecast.forecastday[0].hour[n].temp_c}}°C</span>
                <span style="font-weight: bold">{{results.forecast.forecastday[0].hour[n].temp_f}}°F</span>
                <span>Chuva: {{results.forecast.forecastday[0].hour[n].chance_of_rain}}%</span>
                <span v-if="results.forecast.forecastday[0].hour[n].precip_mm > 0">Precipitação: {{results.forecast.forecastday[0].hour[n].precip_mm}}mm</span>
              </div><!--item-->
            </div><!--rowFlex-->
          </div><!--col-md-6-->
      </div><!--mainrow-->

      <hr />

      <div class="footer">
        <button style="margin: 50px auto;" class="btn btn-primary" v-on:click="unsetLocate">Ver precisão de outra cidade</button>
      </div><!--footer-->
    </div><!--main-->   
  </div><!--container-->
</template>

<script>
  export default {
    name: 'App',

    data() {
      return { 
        results: [],
        results2: [],
        apiKey: '23c7a5af8a8e49968ee224129210102',
        locate: '',
        historico: []
      }
    },

    methods: {
      
      async getData() {
        try {
          const response = await fetch(`http://api.weatherapi.com/v1/forecast.json?key=${this.apiKey}&lang=pt&q=${this.locate}`);

          this.results = await response.json();

        } catch(error) {
          console.log(error)
        }
      },

      // async getForecast() {
      //   try {
      //     const response = await fetch(`http://api.weatherapi.com/v1/forecast.json?key=${this.apiKey}&lang=pt&q=${this.locate}`);

      //     this.results2 = await response.json();

      //   } catch(error) {
      //     console.log(error)
      //   }
      // },

      setLocate() {
        let value = document.querySelector('.form-control').value;
        this.historico.push(value);
        this.locate = value;
        this.getData();
      },

      setLocateHistorico(el) {
        this.locate = el;
        this.getData();
      },

      unsetLocate() {
        this.locate = '';
      },

      removeLocate(index) {
        this.historico.splice(index, 1);
      }
  },

  created() {
    this.getData();
    // this.getForecast();
  }
}
</script>

<style>

  .show {
    display: block;
  }

  .hide {
    display: none;
  }

  /* .btn {
    margin-left: 10px;
  } */

  .btn:hover {
    cursor: pointer;
    background-color: #f5f5f5;
  }

  .cold {
    color: rgb(117, 149, 236);
  }

  .hot {
    color: rgb(236, 82, 44);
  }

  .title {
    text-align: center;
    margin: 10px 0 30px;
  }

  .main .row {
    display: flex;
    flex-wrap: nowrap;
    align-items: center;
  }

  .main .row h2 {
    width: auto;
  }

  .main .row p {
    width: auto;
  }

  .main .row img {
    max-width: 100px;
    width: 100%;
    height: 100%;
  }

  .footer {
    text-align: center;
  }

  ul {
    list-style: none;
    margin: 0;
  }

  ul li p {
    font-size: 24px;
  }

  .rowFlex {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .rowFlex .item {
    width: 200px;
    display: flex;
    flex-direction: column;
    text-align: center;
    padding: 10px 40px;
    margin: 10px;
    box-shadow: 0px 0px 10px 0px rgba(0,0,0,0.75);
    -webkit-box-shadow: 0px 0px 10px 0px rgba(0,0,0,0.75);
    -moz-box-shadow: 0px 0px 10px 0px rgba(0,0,0,0.75);
  }

  .rowFlex .item p {
    font-size: 24px;
    font-weight: bold;
    margin-bottom: -5px;
  }

  .rowFlex .item img {
    margin: 0 auto;
    max-width: 64px;
    max-height: 64px;
    width: 100%;
    height: 100%;
  }
</style>