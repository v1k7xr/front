<template>
    <div>
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
</template>

<script>
import {httpHeader} from '../api/common'
import axios from 'axios'

export default {
    data() {
        return {
            helloString : null,
            cityName : '',
            hoursNumber: 0,
            passengeNumber : 0,  
            activList : []
        }
    },

    methods: {
        testApi() {
            let jsonData = {
                cityName: this.cityName,
                hoursNumber: this.hoursNumber,
                passengeNumber : this.passengeNumber,
                activList : this.activList,
            }

            console.log(jsonData);

            axios
              .post(httpHeader, jsonData)
              .then(response => {
                  console.log(response);
              })
        }, 
    }
}
</script>