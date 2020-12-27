<template>
    <div> 
        <div>
            <div v-if="inputVisible">
                <div class="container">
                <div style="display: flex;align-content: center;width: 100%;justify-content: center">
                    <div >
                    <label  >Город</label>
                    <div class="col-lg-5">
                        <input v-model="cityName" placeholder="Укажите город" style="width: 250px;">
                    </div>
                    </div>
                        </div>  
                    <br>
                    <div style="display: flex;align-content: center;width: 100%;justify-content: center">
                    <div >
                    
                        <label >Количество часов в распоряжении</label>
                    <div class="col-lg-5">
                    <input type="datetime-local" id="start" name="trip-start" style="width: 250px;" >
                        <!--<input v-model="hoursNumber" placeholder="Укажите количество часов" style="width: 250px;">-->
                    </div>
                    </div>
                    </div>
                    <br>
                    <div style="display: flex;align-content: center;width: 100%; justify-content: center">
                        <div >
                        <label >Колличество пассажиров</label>
                        <div class="col-lg-5">
                            <input v-model="passengeNumber" placeholder="Укажите количество пассажиров"  style="width: 250px;">
                        </div>
                        </div>  
                    </div> 
                <br>

                <input type="checkbox" id="onplace" value="onplace" v-model="activList">
                    <label for="onplace">Не выходя из аэропорта</label> <br>

                <input type="checkbox" id="luggage" value="luggage" v-model="activList">
                    <label for="luggage">Я с багажом</label> <br>

                <input type="checkbox" id="hotel" value="hotel" v-model="activList">
                    <label for="hotel">Отели</label> <br>

                <input type="checkbox" id="entertainment" value="entertainment" v-model="activList">
                    <label for="entertainment">Развлечения</label> 
                <input type="checkbox" id="entertainment_kidfriendly" value="entertainment_kidfriendly" v-model="activList">
                    <label for="entertainment_kidfriendly">Можно с детьми</label> <br>
                    <br>

                <input type="checkbox" id="eat" value="eat" v-model="activList">
                    <label for="eat">Покушать</label> <br>

                <input type="checkbox" id="interesting_places" value="interestingPlaces" v-model="activList">
                    <label for="interesting_places">Интересные места</label> <br>

                <button v-on:click="testApi">Тест API</button>
            </div>
            </div>

            <div v-if="loading"> 
                <p> Загрузка </p>
            </div>

            <div v-if="activitiesVisible"> 
                <p> Активности </p>
                <div v-for="item of parsedActivList" :key="item">
                    <input :value="item" name="item" type="checkbox" v-model="item.choosed" @change="getLonLatById($event, item.id)"/>
                    <p> Название: {{ item.name }} ({{ item.type }}) </p>
                    <p> Адрес: {{ item.location }} </p> 
                    <p> Рейтинг: {{ item.raitnig }} {{item.countvoted}} </p>
                    <br>
                </div>

                <button v-on:click="computePath">Рассчитать маршрут</button>
                <div class="map_conteiner">
                    <google-map :name="name"> </google-map>
                    
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import {httpHeader} from '../api/common'
import googleMap from './googleMap'
// import { loadYmap } from 'vue-yandex-maps'

import axios from 'axios'

// const settings = {
//   apiKey: '',
//   lang: 'ru_RU',
//   coordorder: 'latlong',
//   version: '2.1'
// }


export default {
    data() {
        return {
            map:null,
            mapCenter: {lat:0, lng:0},
            inputVisible : true,
            activitiesVisible : false,
            loading : false,
            helloString : null,
            cityName : '',
            hoursNumber: 0,
            passengeNumber : 0,  
            activList : [],
            parsedActivList : [],
            choosedActives : [],
            name: 'map',
            
        }
    },

    mounted: async function () {
            let recaptchaScript = document.createElement('script')
            recaptchaScript.setAttribute('src', 'https://maps.googleapis.com/maps/api/js?key=')
            await document.head.appendChild(recaptchaScript)
    },
    components: {googleMap},

    methods: {

        async testApi() {
            
            let jsonData = {
                cityName: this.cityName,
                hoursNumber: this.hoursNumber,
                passengeNumber : this.passengeNumber,
                activList : this.activList,
            }

            this.inputVisible = false;
            this.loading = true;

            

            await axios
              .post(httpHeader, jsonData)
              .then(response => {

                  let i = 0;
                  let tempR = response['data']['data'];
                    tempR.forEach(element => {
                        this.parsedActivList.push({
                            id : i,
                            location : element['location'],
                            name : element['name'],
                            raitnig : element['raiting'],
                            type : element['type'],
                            lan : element['lan'],
                            lon : element['lon'],
                            countvoted : element['countofvoted'],
                            choosed : false,
                        })
                        i += 1;
                    });

              })
            
            this.loading = false;
            this.activitiesVisible = true;
        },

        computePath() {
            
        },

        getLonLatById(event, id) {
            if (this.parsedActivList[id]['choosed'] == false) {
                this.choosedActives = this.choosedActives.filter(function(el) { return el.id != id; });
            } else {
                this.choosedActives.push(this.parsedActivList[id])

                console.log('add')
            }
            console.log(this.choosedActives);
            console.log(event)
            console.log(id)
        },

    }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
Button{
border-radius: 4px;
-webkit-box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.08);
-moz-box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.08);
box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.08);
color: #fff;
display:block;
width:200px;
text-align: center;
font-family: Arial, Helvetica, sans-serif;
font-size: 14px;
padding: 8px 16px;
margin: 20px auto;
text-decoration: none;
text-shadow: 0 1px 1px rgba(0, 0, 0, 0.075);
-webkit-transition: background-color 0.1s linear;
-moz-transition: background-color 0.1s linear;
-o-transition: background-color 0.1s linear;
transition: background-color 0.1s linear;        
}
Button {
background-color: #5b9cfc;
border: 1px solid rgb( 33, 126, 74 );
}
        
Button:hover {
background-color: rgb( 229, 240, 255);
}




.checkbox {
    position:relative;
    padding-left:25px;
}
.checkbox input[type=checkbox] {
    display:none;
}
.checkbox label:after {
    content:'';
    display:block;
    height:14px;
    width:14px;
    outline:1px solid #939598;
    position:absolute;
    top:0;
    left:0;
}
.checkbox input[type=checkbox]:checked + label:after {
    outline:1px solid #939598;
    border:2px solid #fff;
    width:10px;
    height:10px;
    background-color:#63849F;
}
</style>