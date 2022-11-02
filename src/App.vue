<template>
  <div id="app" class="hh">
    <main>
      <div class="search-box">
        <select class = "city" v-model="query"> </select>
      </div>
      <div class="weather-wrap" v-if="typeof data.main != 'undefined'">
        <div id="app1" class="info" :class="{ maxTemp: maxTemp, minTemp: minTemp }" >
          <div class="location-temp" >
            <p class="location">{{ query}}</p>
            <p class="temp">{{ Math.round(data.main.temp) }}°C</p>
          </div>
          <div class="weather-wind">
            <p class="weather">{{ data.weather[0].main }}</p>
            <p class="wind">{{ data.wind.speed }} м/с</p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<!-- SCRIPTS -->
<script>
export default {
  name: "App",
  data() {
    return {
      data: [],
      maxTemp: 0,
      minTemp: 0,
      query: '',
    }
  },
  
 mounted()  {
  let city =  document.querySelector('.city')//к сожалению согрешил и использовал это :(
  fetch('https://restcountries.com/v3.1/all')//получаем данные городов
  .then(function (resp1) {
    return resp1.json()
  })
  .then(function (data1) {
    let output = '';
    data1.forEach(function (city1) {//цикл для заполнения городов в селект
      output += `<option class="option">${city1.capital}</option>`;
      city.innerHTML = output;
    });
  })
  //обновляем данные каждую минуту
  setInterval(() => {this.serach()}, 60000)
      this.query = sessionStorage.query;//сохранение данных селекта
  },
  watch: {
    query(weather) {
      sessionStorage.query = weather;//получение данных селекта
       this.serach()//после получения выполняем функцию парса
    }
  },

  methods: {
    serach() {//функция для парса данных
  
       fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.query}&units=metric&APPID=b716e8d1dec234250048fd364cfa9c6f`
      )
      
        .then((res) => {//превращаем данные в JSON
          return res.json();
        })
        
        .then(this.setResults);
    },
    setResults(results) {
      this.data = results;
      //Смена цвета при определенной температуре
      try{
        if(Math.round(results.main.temp)>20){
        this.maxTemp = 1
        this.minTemp = 0
        }
        else if(Math.round(results.main.temp)<0){
        this.minTemp = 1
        this.maxTemp = 0
      }
      else{
        this.minTemp = 0
        this.maxTemp = 0
      }
      } catch(e){//Обработка ошибки если города нет
        if(this.query!=undefined)
        {
        alert("Информации по городу "+ this.query + " Нет")
        }
      }
    console.log(results)
    console.log(this.query)
    }
  }
 
};

</script>

<!-- STYLING -->
<style>
.location-box .location {
  font-size: 32px;
  text-align: center;
}
.info {
  position: fixed;
  top: 30%;
  display: flex;
  justify-content: space-between;
  width: 200px;
  border: 1px solid black;
  border-radius: 15px;
  padding: 0 30px;
}
.info .temp {
  font-size: 30px;
  margin: 0;
  font-weight: bold;
}

.weather-wind {
  margin-top: 12px;
}
.maxTemp{
  position: fixed;
  top: 30%;
  display: flex;
  justify-content: space-between;
  width: 200px;
  border: 1px solid black;
  border-radius: 15px;
  padding: 0 30px;
  background-color: #FFFFCC;
}
.minTemp{
  position: fixed;
  top: 30%;
  display: flex;
  justify-content: space-between;
  width: 200px;
  border: 1px solid black;
  border-radius: 15px;
  padding: 0 30px;
  background-color: #CCFFFF;
}
</style>
