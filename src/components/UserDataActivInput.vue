<template>
    <div> 
        <div>
            <div v-if="inputVisible">
                <input v-model="cityName" placeholder="Название города">
                <br>
                <input v-model="hoursNumber" placeholder="Колличество часов">
                <br>
                <input v-model="passengeNumber" placeholder="Колличество пассажиров">
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

            <div v-if="loading"> 
                <p> Загрузка </p>
            </div>

            <div v-if="activitiesVisible"> 
                <p> Активности </p>
                <div v-for="item of parsedActivList" :key="item">
                    <input :value="item" name="item" type="checkbox" v-model="choosedActives[item.id]" />
                    <p> Название: {{ item.name }} ({{ item.type }}) </p>
                    <p> Адрес: {{ item.location }} </p> 
                    <p> Рейтинг: {{ item.raitnig }} {{item.countvoted}} </p>
                    <br>
                </div>

                <button v-on:click="computePath">Рассчитать маршрут</button>
            </div>
        </div>

    </div>
</template>

<script>
import {httpHeader} from '../api/common'
import axios from 'axios'


export default {
    data() {
        return {
            inputVisible : true,
            activitiesVisible : false,
            loading : false,
            helloString : null,
            cityName : '',
            hoursNumber: 0,
            passengeNumber : 0,  
            activList : [],
            parsedActivList : [],
            choosedActives : []
        }
    },

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
            console.log(this.parsedActivList);
        },

        computePath() {
            this.parsedActivList.forEach(el => {
                console.log(el);
            })
        }
    }
}
</script>