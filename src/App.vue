<template>
  <div class="wrapper">
    <div class="header">
      <h1>Weather forecast</h1>
      <h4>
        Check current weather in {{ city == "" ? "your city." : cityName }}
      </h4>
      <div>
        <input type="text" v-model="city" placeholder="Enter city name here" />
        <button v-if="city != ''" @click="getWeather()">Check</button>
        <button disabled v-else>Enter city name first</button>
        <p class="error">{{ error }}</p>
      </div>
    </div>

    <div class="weather-wrapper" v-if="info != null">
      <div class="current-weather-container">
        <div class="time-container">
          <h3>Current weather at:</h3>
          <p>{{ getCurrentTime }}</p>
        </div>

        <div class="weather-container">
          <img
            :src="showIcon"
            class="weather-icon"
            alt="Current weather icon"
          />
          <p class="temp-title">{{ showTemp }}<sup>°c</sup></p>
          <div class="weather-description">
            <h3>{{ showDes }}</h3>
            <p>{{ showFeelsLike }}<span></span></p>
          </div>
        </div>

        <p class="min-max-temp">{{ showMinMaxTemp }}</p>

        <div class="addInfo">
          <div>
            <h4>Wind:</h4>
            <p>{{ showWind }}</p>
          </div>
          <div>
            <h4>Humidity:</h4>
            <p>{{ showHumidity }}</p>
          </div>
          <div>
            <h4>Visibility:</h4>
            <p>{{ showVisibility }}</p>
          </div>
          <div>
            <h4>Pressure:</h4>
            <p>{{ showPressure }}</p>
          </div>
          <div>
            <h4>Sunrise:</h4>
            <p>{{ showSunRise }}</p>
          </div>
          <div>
            <h4>Sunset:</h4>
            <p>{{ showSunSet }}</p>
          </div>
        </div>
      </div>

      <div class="forecast-wrapper">
        <h2 class="forecast-title">
          Forecast for 5 days (in 3 hour increments)
        </h2>
        <div class="forecastList">
          <ForecastList
            v-for="item in getForecast"
            :key="item.id"
            :item="item"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ForecastList from "@/components/ForecastList.vue";

export default {
  components: { ForecastList },
  data() {
    return {
      city: "",
      error: "",
      info: null,
      forecast: null,
    };
  },
  computed: {
    cityName() {
      return "«" + this.city + "».";
    },

    getCurrentTime() {
      return this.converteTime(new Date());
    },

    showIcon() {
      let iconUrl =
        "http://openweathermap.org/img/w/" + this.info.weather[0].icon + ".png";
      return iconUrl;
    },

    showTemp() {
      return this.info.main.temp.toFixed();
    },

    showDes() {
      return this.info.weather[0].main;
    },

    showFeelsLike() {
      return "Feels like: " + this.info.main.feels_like.toFixed() + "°";
    },

    showMinMaxTemp() {
      return (
        "Max. temperature - " +
        this.info.main.temp_max.toFixed() +
        "° / Min. temperature - " +
        this.info.main.temp_min.toFixed() +
        "°"
      );
    },

    showWind() {
      return this.info.wind.speed.toFixed() + " м/с";
    },

    showHumidity() {
      return this.info.main.humidity + "%";
    },

    showVisibility() {
      return (this.info.visibility / 1000).toFixed() + " км";
    },

    showPressure() {
      return this.info.main.pressure + " мм";
    },

    showSunRise() {
      return this.converteTime(new Date(this.info.sys.sunrise * 1000));
    },

    showSunSet() {
      return this.converteTime(new Date(this.info.sys.sunset * 1000));
    },

    getCoord() {
      return "lat=" + this.info.coord.lat + "&lon=" + this.info.coord.lon;
    },

    getForecast() {
      this.fetchForecast();
      return this.forecast;
    },
  },
  methods: {
    getWeather() {
      if (this.city.trim().length < 2) {
        this.error = "The city name cannot be less than one character!";
        return false;
      }

      this.error = "";

      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=e8efa17da56a80b46dd8e0b1bd7e60ef`
        )
        .then((res) => (this.info = res.data))
        .catch((error) => {
          error.response.status == 404
            ? (this.error = `We couldn't find a city called ${this.city}.`)
            : (this.error =
                "Oops, something went wrong. Check the weather later.");
        });
    },

    fetchForecast() {
      axios
        .get(
          `https://api.openweathermap.org/data/2.5/forecast?${this.getCoord}&units=metric&cnt=40&appid=e8efa17da56a80b46dd8e0b1bd7e60ef`
        )
        .then((res) => (this.forecast = res.data.list));
    },

    converteTime(time) {
      if (time) {
        return (
          (String(time.getHours()).length == 1
            ? "0" + time.getHours()
            : time.getHours()) +
          ":" +
          (String(time.getMinutes()).length == 1
            ? "0" + time.getMinutes()
            : time.getMinutes())
        );
      }
    },
  },
};
</script>

<style scoped>
.wrapper {
  max-width: 80%;
  min-width: 425px;
  min-height: 200px;
  max-height: 80%;
  border-radius: 50px;
  padding: 30px;
  margin: 20px;
  display: flex;
  flex-flow: column;
  justify-content: center;
  align-items: center;
  background: rgba(116, 223, 255, 0.521);
  color: #fff;
  -webkit-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
}

.wrapper h1 {
  margin-bottom: 10px;
}

.wrapper h4 {
  margin-bottom: 10px;
}

.wrapper input {
  margin-top: 30px;
  background: transparent;
  border: 0;
  border-bottom: 2px solid #bbbbbbbb;
  color: #fcfcfc;
  font-size: 14px;
  padding: 5px 8px;
  outline: none;
}

.wrapper input:focus {
  border-bottom-color: #ffffffb0;
}

.wrapper button {
  background: #67c7ffe8;
  color: #fff;
  border-radius: 10px;
  border: 2px solid #ffffffb0;
  padding: 10px 15px;
  margin-left: 20px;
  font-size: 16px;
  cursor: pointer;
  transition: transform 500ms ease;
}

.wrapper button:hover {
  transform: scale(1.1) translateY(-5px);
}

.wrapper button:disabled {
  background: #bbbbbbbb;
  cursor: not-allowed;
  font-size: 14px;
}

.header {
  display: flex;
  flex-flow: column;
  align-items: center;
}

.error {
  color: #d03939;
}

.weather-wrapper {
  display: flex;
  flex-flow: column;
  justify-content: center;
  align-items: center;
}

.current-weather-container {
  width: 80%;
  max-width: 1500px;
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
  justify-content: center;
  margin: 20px 0;
  border-radius: 50px;
  padding: 20px;
  background: rgba(116, 223, 255, 0.521);
  color: #fff;
  -webkit-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
}

.time-container {
  display: flex;
  flex-flow: column;
  justify-content: center;
  margin-bottom: 10px;
  text-align: center;
}

.weather-container {
  display: flex;
  flex-flow: row nowrap;
  margin-bottom: 10px;
}

.weather-container p {
  display: flex;
  align-items: center;
}

.weather-icon {
  width: 100px;
  height: 100px;
  margin-right: 10px;
}

.temp-title {
  font-size: 64px;
  text-align: center;
}

.weather-description {
  display: flex;
  flex-flow: column;
  justify-content: center;
  margin-left: 15px;
}

.min-max-temp {
  text-align: center;
}

.addInfo {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  width: 50%;
  min-width: 200px;
  margin-top: 20px;
  text-align: center;
}

.addInfo div {
  margin: 10px;
}
.addInfo h3 {
  margin-bottom: 5px;
}

.forecast-wrapper {
  display: flex;
  flex-flow: column;
  justify-content: center;
  align-items: center;
}

.forecast-title {
  margin: 20px 0 10px 0px;
  text-align: center;
}

.forecastList {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
}

@media (max-width: 768px) {
  .wrapper {
    padding: 10px;
    margin: 10px;
  }
  .wrapper input {
    font-size: 12px;
  }

  .wrapper button {
    font-size: 14px;
  }

  .wrapper button:disabled {
    font-size: 12px;
  }

  .weather-container {
    flex-flow: column nowrap;
    margin-bottom: 20px;
  }

  .current-weather-container {
    width: 60%;
  }

  .temp-title {
    font-size: 50px;
    margin-bottom: 25px;
  }

  .min-max-temp {
    width: 50%;
    line-height: 20px;
  }
}
</style>
