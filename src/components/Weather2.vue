<template>
    
    <div class="container">

        <b-modal ref="modalGetCity" hide-footer title="Using Component Methods" >

            <div class="container">

                <div style="margin-bottom: 20px;" class="row">
                    <div class="col-lg-12">
                        <multi-select label="country" @input="getCities" placeholder="Введите страну" selectLabel="кликните для выбора" selectedLabel="Выбрано" deselectLabel="кликните для удаления"  v-model.trim="сountry" :options="optionsCountry" >
                            <span slot="noResult">Страна не найден</span>
                        </multi-select>
                    </div>
                </div>

                <div class="row">
                    <div class="col-lg-12">
                    <multi-select label="city" placeholder="Введите город" selectLabel="кликните для выбора" selectedLabel="Выбрано" deselectLabel="кликните для удаления"  v-model="citySelect" :options="optionsCity" >
                        <span slot="noResult">город не найден</span>
                    </multi-select>
                    </div>
                </div>

                <b-button class="mt-3" variant="outline-success" block @click="changeCity">Cменить город</b-button>
            </div>
        </b-modal>


        <div class="row header">

            <div class="col-lg-6">
                <div class="top-menu" >
                    <h1>
                        <span  v-if="load" >{{ cityRu }}</span>
                        <span v-if="!load" >
                            <b-spinner variant="warning" type="grow" label="Spinning" />
                            <b-spinner variant="primary" type="grow" label="Spinning" />
                            <b-spinner variant="success" type="grow" label="Spinning" />
                            <b-spinner variant="danger" type="grow" label="Spinning" />
                            <b-spinner variant="success" label="Spinning" />
                        </span>
                    </h1>
                    <b-nav>
                        <b-nav-item class="top-link" @click="showModal" >Сменить город</b-nav-item>
                        <b-nav-item class="top-link" @click="getLocation" >Мое местоположение</b-nav-item>
                    </b-nav>
                </div>
            </div>

            <div class="col-lg-6">
                <b-button-group class="float-lg-right" >
                    <b-button variant="success" @click="setCelsius" >°C</b-button>
                    <b-button variant="warning" @click="setFahrenheit" >°F</b-button>
                </b-button-group>
            </div>


        </div>

        <div class="row main">
            <div class="col-lg-12">
                <div class="pog">
                    <div >
                        <img  width="100"  height="200" :src="weather.icon" />
                        <span class="degree" >{{ degree }}°</span>
                    </div>
                    <div class="description-weather">{{ weather.description }}</div>
                </div>
            </div>
        </div>

        <div class="row footer">

            <div class="col-lg-3">
                <h6 class="title-params"> ветер</h6>
                <h5>{{ weather.wind_speed }} <span class="des-params">м/с {{ getWindDirections }}</span></h5>
            </div>

            <div class="col-lg-3">
                <h6 class="title-params">Давление</h6>
                <h5>{{weather.pressure }} <span class="des-params">мм рт.ст.</span></h5>
            </div>

            <div class="col-lg-3">
                <h6 class="title-params">Влажность</h6>
                <h5>{{ weather.humidity }}<span class="des-params"> %</span></h5>
            </div>

            <div class="col-lg-3">
                <h6 class="title-params">Верояиность дождя </h6>
                <h5>10 % </h5>
            </div>
          </div>

    </div>

</template>

<script>

    import axios from 'axios'
    import Multiselect from 'vue-multiselect'

    export default {

        name: 'weather2',
        components:{
           "multi-select":Multiselect
        },
        data(){
            return {

                сountry:'',
                optionsCountry:[],
                citySelect:{city_ru:false,city_en:false},
                optionsCity:[],
                weather: {
                    temp:0,
                    description:'',
                    icon:'',
                    wind_speed:0,
                    wind_deg:0,
                    pressure:0,
                    humidity:0,
               },
               degree:0,
               cityEn:'',
               cityRu:'',
               load:true
            }
        },

        computed:{

            getWindDirections(){

                let directions = [
                    {min: 337.5, max: 22.5, name: 'Северный'},
                    {min: 22.51, max: 67.5, name: 'Северо-восточный'},
                    {min: 67.51, max: 112.5, name: 'Восточный'},
                    {min: 112.51, max: 157.5, name: 'Юго-восточный'},
                    {min: 157.51, max: 202.5, name: 'Южный'},
                    {min: 202.51, max: 247.5, name: 'Юго-западный'},
                    {min: 247.51, max: 292.5, name: 'Западный'},
                    {min: 292.51, max: 337.49, name: 'Северо-западный'},
                ];

                for(let i=0;i< directions.length;i++){
                    if(directions[i].min <= this.weather.wind_deg &&  directions[i].max >= this.weather.wind_deg){
                        return directions[i].name;
                    }
                }
            }

        },
        created(){

            this.load = false;
            axios.get('http://localhost:3000/weather/').then((response)=>{
                this.weather = response.data;
                this.degree = Math.round(this.weather.temp-273.15);
                this.cityEn = this.weather.cityEn;
                this.cityRu = this.weather.cityRu;
                this.load = true;
            }).catch((error)=>{ console.log(error); });

            axios.get('http://localhost:3000/country/').then((response)=>{
                this.optionsCountry = response.data;
            }).catch((error)=>{ console.log(error); });


        },

        methods:{

            showModal() {
                this.$refs.modalGetCity.show();
            },

            changeCity() {

                if(this.citySelect.city_en){

                    this.load = false;
                    axios.get('http://localhost:3000/weather/',{params:{city:this.citySelect.city_en}}).then((response)=>{
                        this.weather = response.data;
                        this.degree = Math.round(this.weather.temp-273.15);
                        this.cityEn = this.weather.cityEn;
                        this.cityRu = this.weather.cityRu;
                        this.load = true;
                    }).catch((error)=>{ console.log(error); });
                }

                this.$refs.modalGetCity.hide();

            },
            setCelsius(){

                this.degree = Math.round(this.weather.temp-273.15);

            },
            setFahrenheit(){

                this.degree = Math.round((this.weather.temp-273.15)*9/5+32);

            },
            getCities(oblect){

                axios.get('http://localhost:3000/city/',{params:{country:oblect.country}}).then((response)=>{
                    this.optionsCity = response.data;
                    this.citySelect = this.optionsCity[0];
                }).catch((error)=>{ console.log(error); });

            },
            getLocation() {

                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition((position)=>{

                        this.lat = position.coords.latitude;
                        this.lon = position.coords.longitude;
                        this.load = false;
                        axios.get('http://localhost:3000/weather/',{params:{lat:this.lat,lon:this.lon}}).then((response)=>{
                            this.weather = response.data;
                            this.degree = Math.round(this.weather.temp-273.15);
                            this.cityEn = this.weather.cityEn;
                            this.cityRu = this.weather.cityRu;
                            this.load = true;
                        }).catch((error)=>{ console.log(error); });

                    });
                } else { console.log("Geolocation is not supported by this browser."); }

            }

        }


    }
</script>

<style>

    body {
        background-color: #0089ff;
    }

    .top-link a{

        color:#77bfff !important;
    }

    .header{

        height: 100px;
        margin-top: 60px;
    }

    .main{
        height: 400px;
        border: red solid 1px;

    }

    .footer{

        height: 100px;
    }

    .top-menu h1 {
        position: relative;
        left:10px;
        color: honeydew;
    }

    .pog {
        width: 400px;
        height: 100px;
        margin-right: 60%;
        margin-left: 35%;
    }

    .degree {
        position:relative;
        font-size: 100px;
        bottom:-10px;
        color:honeydew;
    }

    .description-weather{
        position: relative;
        top:-20px;
        left:15px;
        font-weight: bolder;
        color: aliceblue;
        font-family:Verdana,Tahoma;
        font-size: large;
    }
    .description-weather:first-letter{
        text-transform: capitalize;
    }

    .title-params {

        color:#77bfff;
    }

    .des-params {

        color:#a3d4ff;
    }

    h5{ color:#fff; }


</style>


<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>

<style>

    .multiselect__option--highlight {
        background: #0089ff;
        outline: none;
        color: #fff;
    }


</style>

