<template>
    <div>
        <p> {{ helloString }} </p>
        <input v-model="cityName" placeholder="Название города">
        <input v-model="hoursNumber" placeholder="Колличество часов">
        <input v-model="passengeNumber" placeholder="Колличество пассажиров">

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
            activList : {

            }
        }
    },

    methods: {
        testApi() {
            axios
            .get(httpHeader)
            .then(response => (this.helloString = response))
            console.log(httpHeader);
        }
    }
}
</script>