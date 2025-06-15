<template>
    <div class="wrapper">
        <h1>Програма для погоди</h1>
        <p>дізнатися погоду в {{ city == "" ? "вашому місті" : cityName }}</p>
        <input type="text" v-model="city" placeholder="Введіть місто">
        <button v-if="city != ''" @click="getWeather(city)">Отримати погоду</button>
        <button v-else disabled>Введіть назву міста!</button>
        <p class="error" v-if="error">{{ error }}</p>

        <div v-if="info != null" class="weather_block">
            <p><i class="fas fa-temperature-high"></i> {{ showTemp }}</p>
            <p><i class="fas fa-thermometer-half"></i> {{ showFeelsLike }}</p>
            <p><i class="fas fa-tint"></i> {{ showHumidity }}</p>
            <p><i class="fas fa-compress-arrows-alt"></i> {{ showPressure }}</p>
            <p><i class="fas fa-wind"></i> {{ showWind }}</p>
            <p><i class="fas fa-cloud"></i> {{ showClouds }}</p>
            <p><i class="fas fa-eye"></i> {{ showVisibility }}</p>
            <p><i class="fas fa-sun"></i> {{ showSunrise }}</p>
            <p><i class="fas fa-moon"></i> {{ showSunset }}</p>
            <img :src="'https://openweathermap.org/img/wn/' + info.weather[0].icon + '@2x.png'" alt="Weather icon">
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default{
    data(){
        return{
            city:"",
            error:"",
            info: null
        }
    },
    methods:{
        async getWeather(city){
            this.info = null
            if(city.trim().length < 2){
                this.error = "Введіть назву міста більше 1 символу!"
                return false
            }
            this.error = ""

            const response = await axios.get(`https://api.openweathermap.org/geo/1.0/direct?q=${this.city}&limit=5&appid=ea95d7be6facb880f812da37738353fc`)
            if (!response.data.length) {
                this.error = "Такого міста немає в Базі Даних. Спробуйте іншу назву."
                return
            }
            console.log(response.data)
            const weather = await axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${response.data[0].lat}&lon=${response.data[0].lon}&units=metric&appid=ea95d7be6facb880f812da37738353fc`)
            this.info = weather.data
            console.log(weather)
        },
        formatTime(unixTime) {
            const date = new Date(unixTime * 1000);
            return date.toLocaleTimeString("uk-UA", { hour: '2-digit', minute: '2-digit' });
        },
        windDirection(deg) {
            const directions = ['Пн', 'Пн-Сх', 'Сх', 'Пд-Сх', 'Пд', 'Пд-Зх', 'Зх', 'Пн-Зх'];
            const index = Math.round(deg / 45) % 8;
            return directions[index];
        }
    },
    computed:{
        cityName(){
            return "«" + this.city + "»"
        },
        showTemp(){
            return "Температура: " + this.info.main.temp + "°C"
        },
        showFeelsLike() {
            return "Відчувається як: " + this.info.main.feels_like + "°C";
        },
        showHumidity() {
            return "Вологість: " + this.info.main.humidity + "%";
        },
        showPressure() {
            return "Тиск: " + this.info.main.pressure + " гПа";
        },
        showWind() {
            return "Вітер: " + this.info.wind.speed + " м/с, напрям: " + this.windDirection(this.info.wind.deg);
        },
        showClouds() {
            return "Хмарність: " + this.info.clouds.all + "%";
        },
        showVisibility() {
            return "Видимість: " + (this.info.visibility / 1000) + " км";
        },
        showSunrise() {
            return "Схід сонця: " + this.formatTime(this.info.sys.sunrise);
        },
        showSunset() {
            return "Захід сонця: " + this.formatTime(this.info.sys.sunset);
        }
    }
}
</script>

<style scoped>
.error{
    color: #d03939;
}
.wrapper{
    width:clamp(200px, 70vw, 1200px);
    min-height: 500px;
    border-radius: 50px;
    padding: 20px;
    background: #1f0f24;
    text-align: center;
    color: whitesmoke;
    margin: 100px 0;
}
.wrapper h1{
    margin-top: 50px;
}
.wrapper p{
    margin-top: 20px;
}
.wrapper input{
    margin-top: 30px;
    background-color: transparent;
    border: 0;
    border-bottom:2px solid #110813 ;
    color:#fcfcfc;
    font-size: 14px;
    padding: 5px 8px;
    outline: none;
}
.wrapper input:focus{
    border-bottom-color:#6e2d7d;
}
.wrapper button:disabled{
    background-color: #e34d4d;
    border:2px solid #b93e35;
    cursor: not-allowed;
}
.wrapper button{
    background-color: #e3904d;
    color:#fcfcfc;
    border-radius: 10px;
    border:2px solid #b95a35;
    padding: 10px 15px;
    margin-top: 10px;
    margin-left: 20px;
    cursor:pointer;
    transition: transform 500ms ease;
}
.wrapper button:hover{
    transform: scale(1.1) translateY(-5px);
}
.weather_block p {
    text-align: left;
    margin-left: 20vw;
}
.weather_block img{
    position: relative;
    left:20vw;
}
@media (max-width: 600px) {
    .weather_block p{
        margin-left: 10vw;
    }
}
</style>
