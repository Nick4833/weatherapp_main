<template>
  <div id="app" :class="typeof weather.main != 'undefined' && (weather.main.temp > 16 && weather.main.temp <= 29 ? 'warm' : ''
    || weather.main.temp <= 16 && weather.main.temp > 0 ? 'cold' : '' || weather.main.temp < 0 ? 'freeze' : ''|| weather.main.temp > 29 ? 'hot' : '')">
    
    <main>
      <div class="container" style="padding: 20px">
        <div class="searchbox">
          <input 
          type="text" 
          class="searchbar" 
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
          />
        </div>
      </div>
      <div class="weatherwrap" v-if="typeof weather.main != 'undefined'">
        
        <div class="infobox">
          <div class="column"><i class="fas fa-cloud icon"></i> {{ weather.clouds.all }}%</div>
          <div class="column"><i class="fas fa-wind icon"></i> {{ weather.wind.speed }}m/s</div>
          <div class="column"><i class="fas fa-tint icon"></i> {{ weather.main.humidity }}% </div>
        </div>
        
        <div class="weatherbox">
          <div class="temp">{{ Math.round(weather.main.temp) }}°C</div>
          <div class="tempbox">
            <div class="tempcolumn"></div>
            <div class="tempcolumn"><i class="fas fa-arrow-up"></i> {{ Math.round(weather.main.temp_max) }}°C</div>
            <div class="tempcolumn"><i class="fas fa-arrow-down"></i> {{ Math.round(weather.main.temp_min) }}°C</div>
            <div class="locationbox"></div>
        </div>
        </div>

        <div class="locationbox">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="weathericon"><i :class="`owf owf-${ weather.weather[0].id } owf-4x`"></i></div>
        </div>

        
      </div>
    </main>
  </div>
</template>

<script>


export default {
  name: 'App',
  data () {
    return {
      api_key: '8dfd53a3ab185d00a9b824b55cdfc20a',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      position: null
    }
  },

  created: function() {
    if(navigator.geolocation){
       navigator.geolocation.getCurrentPosition(position => {
        this.position = position.coords;
        fetch(`${this.url_base}weather?lat=${Math.round(this.position.latitude)}&lon=${Math.round(this.position.longitude)}&units=metric&appid=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
      })
    }
  },

  methods: {

    fetchWeather(e) {
      if(e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
      }
    },

    setResults(results) {
      this.weather = results;
    },

    setTime() {
      let today = new Date();
      this.time = today.getHours();
    },

  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Roboto&display=swap');
@import './assets/css/owfont-regular.css';

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  padding: 40px;
  font-family: 'Roboto';
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
  background-color: #494e60;
}

#app {
  background-image: url('./assets/neutral-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: opacity 0.4s;
  width: 420px;
  box-shadow: 2px 2px 16px rgba(0,0,0,0.25);
}

#app.cold {
  background-image: url('./assets/cold-bg.jpg');
}

#app.warm {
  background-image: url('./assets/warm-bg.png');
}

#app.freeze {
  background-image: url('./assets/freeze-bg.jpg');
}

#app.hot {
  background-image: url('./assets/hot-bg.jpg');
}

main {
  height: 800px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
}

.searchbox {
  width: 100%;
  margin-bottom: 10px;
}

.searchbox .searchbar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #000000ec;
  font-size: 18px;
  font-weight: 400;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.searchbox .searchbar:focus {
  box-shadow: 0px 0px 16px rgba(0,0,0,0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.locationbox {
  float:none;
  text-align: center;
}

.locationbox .location {
  color: #FFF;
  font-size: 20px;
  font-weight: 500;
}

.locationbox .weather {
  margin: 10px 0px;
  color: #FFF;
  font-size: 15px;
  font-style: italic;
}

.locationbox .weathericon {
  padding-top: 20px;
  color: #FFF;
  font-size: 40px;
}

.weatherbox {
  text-align: center;
}

.weatherbox .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 100px;
  font-weight: 900;
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 0px 16px 0px 16px;
  margin: 15px 0px;
  box-shadow: 3px 6px rgba(0,0,0,0.25);
}


.column {
  float: left;
  width: 33.33%;
  padding: 10px;
  color: #FFF;
}

.infobox {
  content: "";
  display: table;
  clear: both;
  width: 100%;
  text-align: center;
  margin-bottom: 5px;
}


.tempcolumn {
  float: left;
  width: 25%;
  padding: 10px;
  color: #FFF;
  font-size: 18px;
}

.tempbox {
  content: "";
  display: table;
  clear: both;
  width: 100%;
  text-align: center;
  margin-bottom: 15px;
}

.icon {
  font-size: 25px;
  margin-right: 3px;
}

@media only screen and (max-width: 600px) {
  body {
    padding: 0;
  }
  main {
    height: 100vh;
  }
}
</style>
