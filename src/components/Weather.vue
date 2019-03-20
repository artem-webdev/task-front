<template>


  <div class="grid">
    <div>
      <h1>Омск</h1>
      <div style="display: inline;" >Сменить город</div>
      <div  @click="getLocation" style="display: inline;" >Мое местоположение</div>
    </div>

    <div>
      <div class="pog">
        <div >
          <img  width="100"  height="100" :src="weather_icon" />
          <span style="position:relative;font-size: 100px; bottom: 50px;color:honeydew;" >{{ temp_cel }}</span>
        </div>
        <div class="description-weather">{{ data_weather.weather[0].description }}</div>
      </div>
    </div>
    <div>Item 3</div>
    <div>Item 4</div>
    <div>Item 5</div>
    <div>Item 6</div>
  </div>


</template>

<script>

import axios from 'axios'

export default {

  name: 'weather',

  data(){
     return{
       data_weather:{},
       weather_icon:'',
       temp_cel:0,
       lat:0,
       lon:0

     }
  },

  created(){

      let api_key = "cc7e0e2e24d0a79b409823b4ecf906e7";
      let lang = "ru";
      let url = "https://api.openweathermap.org/data/2.5/weather?q=moscow&lang="+lang+"&appid="+api_key;

      axios.get(url).then((response)=>{
        this.data_weather = response.data;
        this.weather = response.data.weather[0];
        this.weather_icon = "http://openweathermap.org/img/w/"+this.weather.icon+".png";

        this.temp_cel = Math.round(parseFloat(this.data_weather.main.temp)-273.15);
        console.log(this.data_weather);

      }).catch((error)=>{
        console.log(error);
      });

      // Math.round(x)

  },

  methods:{

      getLocation() {

        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition((position)=>{

              this.lat = position.coords.latitude;
              this.lon = position.coords.longitude;

              console.log(this.lat,this.lon);

          });
        } else { console.log("Geolocation is not supported by this browser."); }

      }

    }


}
</script>

<style scoped>

  .grid {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    /*grid-column-gap: 20px;*/
    /*grid-row-gap: 5px;*/
    grid-template-rows: 150px 400px 150px;
  }

  .grid div {
    background-color: #0089ff;
    padding: 10px;
  }

  .grid div:nth-child(1){
      grid-area: auto;
      grid-column-start: 1;
      grid-column-end: 5;
   }

  .grid div:nth-child(2){
    grid-column-start: 1;
    grid-column-end: 5;
  }

  .pog {
    width: 400px;
    height: 100px;
    margin-right: 60%;
    margin-left: 35%;
  }

  .description-weather{
    position: relative;
    top:-50px;
    left:15px;
    font-weight: bolder;
    color: aliceblue;
    font-family:Verdana,Tahoma;
    font-size: large;
  }
  .description-weather:first-letter{
    text-transform: capitalize;
  }

</style>
