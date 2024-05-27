<template>
    <div class="wrapper">
        <div class="hat">
            <h1>Погодная страница</h1>
            <p>Узнать погоду в {{ city == "" ? "вашем городе" : cityName }}</p>
        </div>
        <input type="text" v-model="city" placeholder="Введите город" list="cityname">
            <datalist id="cityname">
                <option v-for="item in citylist" v-bind:value="item.city">{{ item.region }}</option>
            </datalist>
        <button v-if="city != ''" v-on:click="getWeather()">Узнать погоду</button>
        <button disabled v-else>Узнать погоду</button>
        <p v-if="error != ''" class="error">{{ error }}</p>
        <div v-if="info != null" class="cityBlock" @mouseenter="iconchangewhite()" @mouseleave="iconchangeblack()">
            <table class="dataTable">
                <tr>
                    <th><img v-bind:src="'../src/assets/images/TempCur-' + tempCur + '.png'" width="35" height="35"/></th>
                    <th size="12">Температура сейчас</th>
                    <th>{{ info.temp }}</th>
                    <th><img v-bind:src="'../src/assets/images/Feels-' + tempCur + '.png'" width="35" height="35"/></th>
                    <th size="12">Ощущается как</th>
                    <th>{{ info.feelsLike }}</th>
                </tr>
                <tr>
                    <th><img v-bind:src="'../src/assets/images/TempMin-' + tempCur + '.png'" width="40" height="40"/></th>
                    <th size="12">Минимальная температура</th>
                    <th>{{ info.tempMin }}</th>
                    <th><img v-bind:src="'../src/assets/images/Wind-' + tempCur + '.png'" width="40" height="40"/></th>
                    <th size="12">Ветер (м/с)</th>
                    <th>{{ info.windspeed }}</th>
                </tr>
                <tr>
                    <th><img v-bind:src="'../src/assets/images/TempMax-' + tempCur + '.png'" width="40" height="40"/></th>
                    <th size="12">Максимальная температура</th>
                    <th>{{ info.tempMax }}</th>
                    <th><img v-bind:src="'../src/assets/images/Sky-' + tempCur + '.png'" width="50" height="50"/></th>
                    <th size="12">Осадки</th>
                    <th>{{ info.sky }}</th>
                </tr>
            </table>
        </div>
        <div disabled v-else></div>
    </div>
</template>

<script>
import axios from 'axios'
import cities from './cities/cities.json'

export default {
    data() {
        return {
            city: "",
            error: "",
            info: null,
            citylist: cities.list,
            tempCur: "black"
        }
    },
    computed: {
        cityName() {
            return "городе «" + this.city + "»"
        }
    },
    methods: {
        async getWeather() {
            if (this.city.trim().length < 2) {
                this.error = "Название должно быть длиннее одного символа."
                return false
            }
            this.info = null
            this.error = ""
            const apiUrl = 'https://api.openweathermap.org/data/2.5/weather?q=' + this.city + '&units=metric&appid=367c5388e79299b1c73a510405463114'
            try {
                const res = await axios.get(apiUrl)
                console.log('Успешный запрос', res.data.cod)
                let st = ""
                if(res.data.weather[0].main == "Clear") {
                    st = "Чисто"
                }
                else if (res.data.weather[0].main == "Clouds") {
                    st = "Облачно"
                }
                else if (res.data.weather[0].main == "Snow") {
                    st = "Снег"
                }
                else if (res.data.weather[0].main == "Rain") {
                    st = "Дождь"
                }
                else {
                    st = "Не установленно"
                }
                this.info = {
                    code: res.data.cod,
                    tempMin: res.data.main.temp_min,
                    tempMax: res.data.main.temp_max,
                    temp: res.data.main.temp,
                    feelsLike: res.data.main.feels_like,
                    windspeed: res.data.wind.speed,
                    sky: st
                }
            }
            catch (error) {
                console.log('Неудачный запрос', error.response.status)
                this.info = null
                if(error.response.status){
                    this.error = "Такой город не найден."
                }
                return false
            }
        },
        iconchangewhite() {
            this.tempCur = "white"
        },
        iconchangeblack() {
            this.tempCur = "black"
        }
    }
}
</script>

<style scoped>
    th {
        font-family: 'Franklin Gothic Medium';
        background: #1f97ce;
        border-radius: 4px;
        min-width: 50px;
        height: 50px;
    }
    .wrapper {
        width: 900px;
        height: 420px;
        border-radius: 50px;
        padding: 20px;
        background-color: rgb(236, 236, 236);
        text-align: center;
        color: rgb(0, 0, 0);
    }
    .wrapper h1 {
        margin-top: 5px;
        font-family: 'Franklin Gothic Medium';
    }
    .wrapper p {
        margin-top: 5px;
        padding-bottom: 5px;
        font-family: 'Franklin Gothic Medium';
    }
    .wrapper input {
        margin-top: 30px;
        background: transparent;
        border: 0;
        border-bottom: 2px solid #110813;
        color: #777777;
        font-size: 14px;
        padding: 5px 8px;
        outline: none;
        transition: transform 500ms ease;
    }
    .wrapper input:focus {
        border-bottom-color: #4a3dff;
    }
    .wrapper button {
        background: #00d9ff;
        color: #110813;
        border-radius: 10px;
        border: 2px solid #00d3f8;
        padding: 10px 15px;
        margin-left: 20px;
        cursor: pointer;
        transition: transform 500ms ease;
    }
    .wrapper button:hover {
        transform: scale(1.1) translateY(-5px);
        background: #4a3dff;
        border: 2px solid #4539ee;
        color: #ffffff;
    }
    .wrapper button:active {
        background: #756cff;
        border: 2px solid #6f68d4;
    }
    .wrapper button:disabled {
        background: #d6d6d6;
        border: 2px solid #bebebe;
        cursor: not-allowed;
    }
    .error {
        color: #af1515
    }
    .hat {
        background: #23a6e2;
        border-radius: 20px;
        margin: 2%;
        transition: transform 500ms ease;
    }
    .hat:hover {
        background: #4a3dff;
        margin: 2%;
        transform: scale(1.02) translateY(-5px);
        color: #ffffff;
    }
    .dataTable {
        margin-left: 10%;
        margin-right: 10%;
        border-color: #110813;
    }
    .cityBlock {
        background: #23a6e2;
        border-radius: 20px;
        margin-top: 2%;
        margin-bottom: 2%;
        margin-left: 20%;
        margin-right: 20%;
        padding-top: 1%;
        padding-bottom: 1%;
        transition: transform 500ms ease;
    }
    .cityBlock:hover {
        background: #4a3dff;
        transform: scale(1.02) translateY(-5px);
        color: #ffffff;
    }

</style>
