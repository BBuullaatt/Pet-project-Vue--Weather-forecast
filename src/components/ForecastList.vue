<template>
  <div class="forecastItem">
    <h3>{{ showDate }}</h3>
    <p>{{ showTime }}</p>

    <div class="icon-temp-wrapper">
      <img :src="showIcon" class="forecas-icon" alt="Current weather icon" />

      <div class="temp-wrapper">
        <p>{{ showMaxTemp }}</p>
        <p>{{ showMinTemp }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["item"],
  data() {
    return {
      todayWeekDay: new Date().toLocaleString("en-us", { weekday: "long" }),
    };
  },
  computed: {
    showDate() {
      return this.todayWeekDay ==
        new Date(this.item.dt * 1000).toLocaleString("en-us", {
          weekday: "long",
        })
        ? "Today"
        : new Date(this.item.dt * 1000).toLocaleString("en-us", {
            weekday: "long",
          });
    },

    showTime() {
      return this.item.dt_txt.slice(11, 16);
    },

    showIcon() {
      let iconUrl =
        "http://openweathermap.org/img/w/" + this.item.weather[0].icon + ".png";
      return iconUrl;
    },

    showMaxTemp() {
      return this.item.main.temp_max.toFixed() + "°";
    },

    showMinTemp() {
      return this.item.main.temp_min.toFixed() + "°";
    },
  },
  methods: {},
};
</script>

<style scoped>
.forecastItem {
  width: 105px;
  display: flex;
  flex-flow: column;
  background: rgba(116, 223, 255, 0.466);
  color: #fff;
  -webkit-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
  margin: 20px;
  padding: 5px;
  border-radius: 10px;
  text-align: center;
}

.icon-temp-wrapper {
  display: flex;
  justify-content: center;
  margin-top: 5px;
}

.forecas-icon {
  margin-right: 5px;
}

.temp-wrapper {
  display: flex;
  flex-flow: column;
  justify-content: center;
}
</style>
