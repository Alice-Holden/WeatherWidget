<template>
<div class="city">
  <h4>{{city.name}}, {{city.country}}</h4>
  <div class="city__temp">
    <!--  icon by Icons8-->
    <img
        :src="`http://openweathermap.org/img/wn/${city.icon}.png`"
        alt="icon"
    />
    <h2>{{ city.temp }} °C</h2>
  </div>
  <p>{{city.description}}</p>
  <div class="flex">
    <div class="flex">
      <img
          class="small-icon"
          :style="{transform: `rotate(${city.wind_direction}deg)`}"
          src="/icon/navigate.png"
          alt="navigate"
      />
      {{ city.wind }}
    </div>
    <div class="flex">
      <img
          class="small-icon"
          src="/icon/humidity.png"
          alt="humidity"
      />
        {{ city.hpa}} hPa
    </div>
  </div>
  <div class="flex">
    <div>Humidity: {{ city.humidity }} %</div>
    <div>Dew point: {{ city.dew_point }} °C</div>
  </div>
  <div class="flex">Visibility: {{city.visibility}} km</div>
</div>
</template>

<script>
export default {
  name: "CityWeather",
  props: {
    city: {
      type: Object,
      required: true
    }
  },
}
</script>

<style scoped lang="scss">
.city{
  width: 250px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  &__temp{
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
    img{
      width: 50px;
      height: 50px;
      -webkit-filter: drop-shadow(1px 1px 1px #222);
      filter: drop-shadow(1px 1px 1px #222);
    }
  }

}
.flex{
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 5px;
  font-size: 12px;
  margin-top: 10px;
}
.small-icon{
  width: 14px;
}
</style>