<script setup>
//https://www.youtube.com/watch?v=8pn9KEuXG28
//https://www.youtube.com/watch?v=s9URD3PefTk
//https://www.youtube.com/watch?v=JYfiaSKeYhE
//https://www.youtube.com/watch?v=Kt2E8nblvXU
//https://gist.github.com/stellasphere/9490c195ed2b53c707087c8c2db4ec0c
import { ref, reactive, onMounted, watch } from "vue";

// stores weather hourly_units "time": "iso8601",  "temperature_2m": "°C"
const weatherunits = reactive({
  temp: "",
  humidity: "",
  precipitation_probability: "",
  apparent_temperature: "",
  wind: "",
});

// stores weather hourly values
const weather_hourly = reactive({
  time: [],
  temp: [],
  humidity: [],
  precipitation_probability: [],
  apparent_temperature: [],
  wind: [],
  weathercode: [],
});

// store the daily weather data
const weather_daily = reactive({
  time: [],
  weather_code: [],
  temperature_2m_max: [],
  apparent_temperature_max: [],
});

// store the daily data units
const weather_daily_units = reactive({
  temperature_2m_max: "",
});

// store the current  weather data
const weather_current = reactive({
  time: "",
  temperature_2m: "",
  relative_humidity_2m: "",
  apparent_temperature: "",
  wind_speed_10m: "",
  is_day: "",
  weather_code: "",
});

// store the current  weather data units
const weather_current_units = reactive({
  temperature_2m: "",
  relative_humidity_2m: "",
  apparent_temperature: "",
  wind_speed_10m: "",
});

//stores all openmetero geocoding result
const georesult = reactive({
  list: [],
});
//stores the final result that will be use for the search function and weather name
const georesultfinal = reactive({
  list: [],
});

// use the value for georesult.list array index
let listvar = ref(27);

//used by Show Weather
function forfinalresult() {
  console.log(listvar);
  console.log("HELLO");
  console.log(listvar.value);
  //assign the name for weather fetch api name
  georesultfinal.list = georesult.list[listvar.value];
  console.log(georesultfinal.list);
  console.log("georesultfinal.list output above");
  //use to make the search result disappear
  listvar.value = 26;
  openmeteogeo.name = "";
  mainweatherfetch();
}

//open meteo weather link the lat and long are changed by getting new output from the search openmeteogeo geocoding
//Hourly Weather Variables
//
const openmeteo = reactive({
  https: "https://api.open-meteo.com/v1/forecast?latitude=",
  lat: "14.6042",
  lotmain: "&longitude=",
  long: "120.9822",
  hourly:
    "&hourly=temperature_2m,wind_speed_10m,relative_humidity_2m,precipitation_probability,apparent_temperature,weather_code&",
  daily: "&daily=weather_code,temperature_2m_max,apparent_temperature_max",
  current:
    "&current=temperature_2m,relative_humidity_2m,apparent_temperature,wind_speed_10m,is_day,weather_code",
  timezone: "&timezone=Asia%2FSingapore",
  //current &current=temperature_2m,relative_humidity_2m,apparent_temperature,wind_speed_10m
  //hourly=temperature_2m,wind_speed_10m,relative_humidity_2m,precipitation_probability,apparent_temperature,weather_code&timezone=Asia%2FSingapore
});

const wmo = reactive({
  0: {
    day: {
      description: "Sunny",
      image: "http://openweathermap.org/img/wn/01d@2x.png",
    },
    night: {
      description: "Clear",
      image: "http://openweathermap.org/img/wn/01n@2x.png",
    },
  },
  1: {
    day: {
      description: "Mainly Sunny",
      image: "http://openweathermap.org/img/wn/01d@2x.png",
    },
    night: {
      description: "Mainly Clear",
      image: "http://openweathermap.org/img/wn/01n@2x.png",
    },
  },
  2: {
    day: {
      description: "Partly Cloudy",
      image: "http://openweathermap.org/img/wn/02d@2x.png",
    },
    night: {
      description: "Partly Cloudy",
      image: "http://openweathermap.org/img/wn/02n@2x.png",
    },
  },
  3: {
    day: {
      description: "Cloudy",
      image: "http://openweathermap.org/img/wn/03d@2x.png",
    },
    night: {
      description: "Cloudy",
      image: "http://openweathermap.org/img/wn/03n@2x.png",
    },
  },
  45: {
    day: {
      description: "Foggy",
      image: "http://openweathermap.org/img/wn/50d@2x.png",
    },
    night: {
      description: "Foggy",
      image: "http://openweathermap.org/img/wn/50n@2x.png",
    },
  },
  48: {
    day: {
      description: "Rime Fog",
      image: "http://openweathermap.org/img/wn/50d@2x.png",
    },
    night: {
      description: "Rime Fog",
      image: "http://openweathermap.org/img/wn/50n@2x.png",
    },
  },
  51: {
    day: {
      description: "Light Drizzle",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Light Drizzle",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  53: {
    day: {
      description: "Drizzle",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Drizzle",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  55: {
    day: {
      description: "Heavy Drizzle",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Heavy Drizzle",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  56: {
    day: {
      description: "Light Freezing Drizzle",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Light Freezing Drizzle",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  57: {
    day: {
      description: "Freezing Drizzle",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Freezing Drizzle",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  61: {
    day: {
      description: "Light Rain",
      image: "http://openweathermap.org/img/wn/10d@2x.png",
    },
    night: {
      description: "Light Rain",
      image: "http://openweathermap.org/img/wn/10n@2x.png",
    },
  },
  63: {
    day: {
      description: "Rain",
      image: "http://openweathermap.org/img/wn/10d@2x.png",
    },
    night: {
      description: "Rain",
      image: "http://openweathermap.org/img/wn/10n@2x.png",
    },
  },
  65: {
    day: {
      description: "Heavy Rain",
      image: "http://openweathermap.org/img/wn/10d@2x.png",
    },
    night: {
      description: "Heavy Rain",
      image: "http://openweathermap.org/img/wn/10n@2x.png",
    },
  },
  66: {
    day: {
      description: "Light Freezing Rain",
      image: "http://openweathermap.org/img/wn/10d@2x.png",
    },
    night: {
      description: "Light Freezing Rain",
      image: "http://openweathermap.org/img/wn/10n@2x.png",
    },
  },
  67: {
    day: {
      description: "Freezing Rain",
      image: "http://openweathermap.org/img/wn/10d@2x.png",
    },
    night: {
      description: "Freezing Rain",
      image: "http://openweathermap.org/img/wn/10n@2x.png",
    },
  },
  71: {
    day: {
      description: "Light Snow",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Light Snow",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  73: {
    day: {
      description: "Snow",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Snow",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  75: {
    day: {
      description: "Heavy Snow",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Heavy Snow",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  77: {
    day: {
      description: "Snow Grains",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Snow Grains",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  80: {
    day: {
      description: "Light Showers",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Light Showers",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  81: {
    day: {
      description: "Showers",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Showers",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  82: {
    day: {
      description: "Heavy Showers",
      image: "http://openweathermap.org/img/wn/09d@2x.png",
    },
    night: {
      description: "Heavy Showers",
      image: "http://openweathermap.org/img/wn/09n@2x.png",
    },
  },
  85: {
    day: {
      description: "Light Snow Showers",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Light Snow Showers",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  86: {
    day: {
      description: "Snow Showers",
      image: "http://openweathermap.org/img/wn/13d@2x.png",
    },
    night: {
      description: "Snow Showers",
      image: "http://openweathermap.org/img/wn/13n@2x.png",
    },
  },
  95: {
    day: {
      description: "Thunderstorm",
      image: "http://openweathermap.org/img/wn/11d@2x.png",
    },
    night: {
      description: "Thunderstorm",
      image: "http://openweathermap.org/img/wn/11n@2x.png",
    },
  },
  96: {
    day: {
      description: "Light Thunderstorms With Hail",
      image: "http://openweathermap.org/img/wn/11d@2x.png",
    },
    night: {
      description: "Light Thunderstorms With Hail",
      image: "http://openweathermap.org/img/wn/11n@2x.png",
    },
  },
  99: {
    day: {
      description: "Thunderstorm With Hail",
      image: "http://openweathermap.org/img/wn/11d@2x.png",
    },
    night: {
      description: "Thunderstorm With Hail",
      image: "http://openweathermap.org/img/wn/11n@2x.png",
    },
  },
});

//Daily weather variables use for the buttons
/*
const openmeteo_dailyvar = reactive({
  https: "https://api.open-meteo.com/v1/forecast?latitude=",
  lat: "14.6042",
  lotmain: "&longitude=",
  long: "120.9822",
  rest: "&daily=weather_code,temperature_2m_max,apparent_temperature_max&timezone=Asia%2FSingapore",
  //daily=weather_code,temperature_2m_max,apparent_temperature_max&timezone=Asia%2FSingapore,
});
*/

//current temp variables use for the left side of the div
/*
const openmeteo_current = reactive({
  https: "https://api.open-meteo.com/v1/forecast?latitude=",
  lat: "14.6042",
  lotmain: "&longitude=",
  long: "120.9822",
  rest: "&current=temperature_2m,relative_humidity_2m,apparent_temperature,wind_speed_10m&timezone=Asia%2FSingapore",
});
*/
//use for search function, a link for the geo location, name is constatly being changed
let openmeteogeo = reactive({
  https: "https://geocoding-api.open-meteo.com/v1/search?name=",
  name: "",
  counttext: "&count=",
  count: "25",
  rest: "&language=en&format=json", //&countryCode=PH
});

let link = reactive({
  //https://www.openstreetmap.org/#map=13/15.15000/120.58333
  linkname: "https://www.openstreetmap.org/#map=13/",
  add: "/",
});

let fetchtotal = ref(0);
let totalbal = ref(0);

let linkoutput = ref(link.linkname + 15.15084 + link.add + 120.58714);

// if true <p> visible if false not visible
let search_result_state = ref(false);

// fetch value from the api, the openmeteogeo.name is differenct depending on what the use choose
function updatelist() {
  fetchtotal.value = fetchtotal.value + 1;
  console.log(search_result_state);
  fetch(
    openmeteogeo.https +
      openmeteogeo.name +
      openmeteogeo.counttext +
      openmeteogeo.count +
      openmeteogeo.rest
  )
    .then((response) => response.json())
    .then((data) => {
      georesult.list = data.results;
      // georesult.list = georesult.list;
      console.log(data);
      console.log(georesult.list);
    });
}

//https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current=temperature_2m,relative_humidity_2m,apparent_temperature,wind_speed_10m
// it will take the result form the georesultfinal latitude and longitude then update the lat and lang of the needed weather location
let name = ref();
let country = ref();
let mainisused = ref();
mainisused.value = false;
function mainweatherfetch() {
  mainisused.value = true;
  fetchtotal.value = fetchtotal.value + 1;
  name.value = georesultfinal.list.name;
  country.value = georesultfinal.list.country;
  openmeteo.lat = georesultfinal.list.latitude;
  openmeteo.long = georesultfinal.list.longitude;
  weatheroutput.value = true;
  //
  linkoutput.value =
    link.linkname +
    georesultfinal.list.latitude +
    link.add +
    georesultfinal.list.longitude;

  search_result_state.value = false;

  fetch(
    openmeteo.https +
      openmeteo.lat +
      openmeteo.lotmain +
      openmeteo.long +
      openmeteo.hourly +
      openmeteo.daily +
      openmeteo.current +
      openmeteo.timezone
  )
    //"https://api.open-meteo.com/v1/forecast?latitude=14.6042&longitude=120.9822&hourly=temperature_2m&timezone=Asia%2FSingapore"
    .then((response) => response.json())
    .then((data) => {
      const hourly_unitsdata = data.hourly_units; //stores the data from hourly units
      const hourlydata = data.hourly; //stores the data from hourly
      const daily_unitsdata = data.daily_units; //stores the data from daily units
      const daily = data.daily; //stores the data from daily
      const current_unitsdata = data.current_units;
      const current = data.current;

      // start of weather hour by hour fetch

      //stores data from the api to the object weatherunits
      weatherunits.temp = hourly_unitsdata.temperature_2m;
      weatherunits.humidity = hourly_unitsdata.relative_humidity_2m;
      // prettier-ignore
      weatherunits.precipitation_probability = hourly_unitsdata.precipitation_probability;
      weatherunits.apparent_temperature = hourly_unitsdata.apparent_temperature;
      weatherunits.wind = hourly_unitsdata.wind_speed_10m;

      //stores data from the api to the object weather hourly
      weather_hourly.time = hourlydata.time;
      weather_hourly.temp = hourlydata.temperature_2m;
      weather_hourly.humidity = hourlydata.relative_humidity_2m;
      // prettier-ignore
      weather_hourly.precipitation_probability = hourlydata.precipitation_probability;
      weather_hourly.apparent_temperature = hourlydata.apparent_temperature;
      weather_hourly.wind = hourlydata.wind_speed_10m;
      weather_hourly.weathercode = hourlydata.weather_code;

      // end of weather hour by hour fetch

      // start of weather daily fetch

      // daily data
      weather_daily.time = daily.time;
      weather_daily.weather_code = daily.weather_code;
      weather_daily.temperature_2m_max = daily.temperature_2m_max;
      weather_daily.apparent_temperature_max = daily.apparent_temperature_max;

      //daily data unit
      weather_daily_units.temperature_2m_max =
        daily_unitsdata.temperature_2m_max;

      // end of weather daily fetch

      // start of weather current fetch
      // weather current data
      weather_current.time = current.time;
      weather_current.temperature_2m = current.temperature_2m;
      weather_current.relative_humidity_2m = current.relative_humidity_2m;
      weather_current.apparent_temperature = current.apparent_temperature;
      weather_current.wind_speed_10m = current.wind_speed_10m;
      weather_current.is_day = current.is_day;
      weather_current.weather_code = current.weather_code;
      // weather current unit data
      weather_current_units.temperature_2m = current_unitsdata.temperature_2m;
      weather_current_units.relative_humidity_2m =
        current_unitsdata.relative_humidity_2m;
      weather_current_units.apparent_temperature =
        current_unitsdata.apparent_temperature;
      weather_current_units.wind_speed_10m = current_unitsdata.wind_speed_10m;
      // end of weather current fetch

      day = weather_daily.time[0];
      console.log(data);
      console.log("Fetch data.");
    });
}

// if value of the  openmeteogeo.name changes then it will use the function  updatelist()
// value of the openmeteo.name is connected to vmodel search
watch(
  () => openmeteogeo.name,
  (newName, oldName) => {
    if (oldName !== newName) {
      updatelist();
    }
  }
);

let interval = null;
watch(
  () => mainisused.value,
  () => {
    if (mainisused.value && !interval) {
      interval = setInterval(() => {
        totalbal.value++;
        mainweatherfetch();
        console.log(totalbal.value);
      }, 30000);
    }
  }
);

// if value of listvar is not equal to its old value or lest than 25 it will use the fucntion  forfinalresult()
// it will then fetch again new data from the api
watch(
  () => listvar.value,
  (newValue, oldValue) => {
    if (newValue !== oldValue && newValue < 25) {
      forfinalresult();
    }
    if (newValue == 26) {
      search_result_state.value = true;
    }
  }
);

let weatheroutput = ref(false);

// array to use for iteration of vaues

const newday = ref([]);

const one = [
  0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21,
  22, 23,
];
newday.value = one;

const two = [
  24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42,
  43, 44, 45, 46, 47,
];

const three = [
  48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66,
  67, 68, 69, 70, 71,
];

const four = [
  72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90,
  91, 92, 93, 94, 95,
];

const five = [
  96, 97, 98, 99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111,
  112, 113, 114, 115, 116, 117, 118, 119,
];

const six = [
  120, 121, 122, 123, 124, 125, 126, 127, 128, 129, 130, 131, 132, 133, 134,
  135, 136, 137, 138, 139, 140, 141, 142, 143,
];

const seven = [
  144, 145, 146, 147, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 158,
  159, 160, 161, 162, 163, 164, 165, 166, 167,
];

function getDayName(dateStr) {
  return new Date(dateStr).toLocaleDateString("en-US", {
    weekday: "long",
  });
}

// check if its night or day if true the day flase night
function dayornight(value) {
  if (
    value === "06:00" ||
    value === "07:00" ||
    value === "08:00" ||
    value === "09:00" ||
    value === "10:00" ||
    value === "11:00" ||
    value === "12:00" ||
    value === "13:00" ||
    value === "14:00" ||
    value === "15:00" ||
    value === "16:00" ||
    value === "17:00"
  ) {
    return "day";
  }
  return "night";
}

function checkdayornight(val) {
  if (val == 0) {
    return "night";
  }
  {
    return "day";
  }
}

//change the value of array one

let day = ref();

function button1() {
  newday.value = one;
  day = weather_daily.time[0];
}
function button2() {
  newday.value = two;
  day = weather_daily.time[1];
}
function button3() {
  newday.value = three;
  day = weather_daily.time[2];
}
function button4() {
  newday.value = four;
  day = weather_daily.time[3];
}
function button5() {
  newday.value = five;
  day = weather_daily.time[4];
}
function button6() {
  newday.value = six;
  day = weather_daily.time[5];
}
function button7() {
  newday.value = seven;
  day = weather_daily.time[6];
}
</script>

<template>
  <p>Timezone: Asia/Singapore</p>
  <div>
    <!--DIVA   class="search-output-container"-->
    <div class="search-container">
      <input class="search" type="search" v-model="openmeteogeo.name" />
      <div
        class="output-container"
        v-for="(openmeteoarray, index) in georesult.list"
        :key="index"
      >
        <button v-on:click="listvar = index">
          Name: {{ openmeteoarray.name }} Country:{{ openmeteoarray.country }}
        </button>
      </div>
    </div>
    <br /><br /><br />
    <!--DIVB-->
    <!--Hide
    <form v-on:submit.prevent="mainweatherfetch()">
      <div v-if="search_result_state == true">
        
        {{ georesultfinal.list.name }} {{ georesultfinal.list.country }}

        <button>Show Weather</button>
      </div>
     
    </form>
    -->
    <hr />
    <!--Hide
    <div v-if="weatheroutput">
      <h3>{{ name }} {{ country }}</h3>

      
      <button>
        <a v-bind:href="linkoutput" target="_blank">View Location</a>
      </button>

      <br /><br />
-->
    <!-- prettier-ignore -->
    <!--Hide
      <p v-for="(time, index) in weather_hourly.time" :key="index">
        {{ index }} DATE & TIME: {{ time }} 
        TEMP: {{ weather_hourly.temp[index]}}{{ weatherunits.temp }}, 
        HUMIDITY:{{ weather_hourly.humidity[index] }}{{ weatherunits.humidity }}, 
        PRECIPITATION:{{weather_hourly.precipitation_probability[index]}}{{ weatherunits.precipitation_probability }}, 
        APPARENT TEMPERATURE:{{weather_hourly.apparent_temperature[index]}}{{ weatherunits.apparent_temperature }}, 
        WIND:{{weather_hourly.wind[index]}}{{ weatherunits.wind }},
        WEATHER CONDITION: {{ weather_hourly.weathercode[index] }}

        <div v-if="weather_hourly.weathercode[index] == 45">
             <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/fog%404x.png"> 
             <h4>Fog</h4>
        </div>

        <div v-else-if="weather_hourly.weathercode[index] == 3">
            <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/overcast%404x.png"> 
        </div>

        <div v-else-if="weather_hourly.weathercode[index] == 2">
            <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/partly-cloudy%404x.png"> 
        </div>
        <div v-else-if="weather_hourly.weathercode[index] == 1">
            <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/mostly-clear%404x.png"> 
        </div>
         <div v-else-if="weather_hourly.weathercode[index] == 80">
            <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/light-rain%404x.png"> 
        </div>


        <div v-else="">
             <img src="https://raw.githubusercontent.com/Leftium/weather-sense/refs/heads/main/static/icons/airy/thunderstorm%404x.png"> 
        </div>
        
      </p>
    </div>
      -->

    <!--DIVD-->
  </div>
  <!--DIVA-->

  <div v-if="weatheroutput">
    <!-- prettier-ignore -->
    <div class="grid-container" :data-weather="weather_current.weather_code">
    <div class="realtime-container">
      <!--Name Location-->
      <div class="name-realtime-weather">
        <h3>Current Weather</h3>
        <p>{{ name }} {{ country }}</p>
      </div>

      <!--Weather current data .-->
      <div class="current-time-weather">  
        <div class="icon-container">
        <img v-bind:src="wmo[String(weather_current.weather_code)]?.[checkdayornight(weather_current.time)]?.image"> 
        <p> {{ wmo[String(weather_current.weather_code)]?.[checkdayornight(weather_current.time)]?.description }} </p>
      </div>
        <div class="weather-current">
        <br>
        <p>Temperature: {{ weather_current.temperature_2m }}{{ weather_current_units.temperature_2m }}</p>
        <p>Feels Like: {{ weather_current.apparent_temperature }}{{ weather_current_units.apparent_temperature }}</p>
        <p>Humidity: {{ weather_current.relative_humidity_2m }}{{ weather_current_units.relative_humidity_2m }}</p>
        <p>Wind Speed: {{ weather_current.wind_speed_10m }}{{ weather_current_units.wind_speed_10m }}</p>
        <a class="button-view" v-bind:href="linkoutput" target="_blank">View Location</a>
      </div>
      </div>


    </div>

    <div class="grid-secondary-container">
      <div class="buttons">
        <div class="button-container">
        
        <button class="button-style" v-on:click="button1">
         <img v-bind:src="wmo[String(weather_daily.weather_code[0])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[0]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[0])]?.day.description}}</p>
         <p> Temperature: {{ weather_daily.temperature_2m_max[0] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[0] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button2">
         <img v-bind:src="wmo[String(weather_daily.weather_code[1])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[1]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[1])]?.day.description}}</p>
         <p> Temperature: {{ weather_daily.temperature_2m_max[1] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[1] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button3">
        <img v-bind:src="wmo[String(weather_daily.weather_code[2])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[2]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[2])]?.day.description}}</p>
         <p> Temperature: {{ weather_daily.temperature_2m_max[2] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[2] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button4">
          <img v-bind:src="wmo[String(weather_daily.weather_code[3])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[3]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[3])]?.day.description}}</p>
         <p> Temperature: {{ weather_daily.temperature_2m_max[3] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[3] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button5">
          <img v-bind:src="wmo[String(weather_daily.weather_code[4])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[4]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[4])]?.day.description}}</p>
         <p> Temperature:{{ weather_daily.temperature_2m_max[4] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[4] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button6">
          <img v-bind:src="wmo[String(weather_daily.weather_code[5])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[5]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[5])]?.day.description}}</p>
         <p> Temperature:{{ weather_daily.temperature_2m_max[5] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[5] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
        <div class="button-container">
        <button class="button-style" v-on:click="button7"> 
          <img v-bind:src="wmo[String(weather_daily.weather_code[6])]?.day.image"> 
         <h6> {{ getDayName(weather_daily.time[6]) }}</h6>
         <p> {{wmo[String(weather_daily.weather_code[6])]?.day.description}}</p>
         <p> Temperature:{{ weather_daily.temperature_2m_max[6] }}{{ weather_daily_units.temperature_2m_max }}</p>
         <p> Feels like: {{ weather_daily.apparent_temperature_max[6] }}{{ weather_daily_units.temperature_2m_max }}</p>
        </button>
        </div>
      </div>

      <div class="hourbyhour">   
          <div class="hourbyhour-container" v-for="iterate in newday">
              <div class="hourbyhouricon">
                
                <img v-bind:src="wmo[String(weather_hourly.weathercode[iterate])]?.[dayornight(weather_hourly.time[iterate].split('T')[1])]?.image"> 
              </div>
                <div class="values">
                    <p>{{ wmo[String(weather_hourly.weathercode[iterate])]?.[dayornight(weather_hourly.time[iterate].split('T')[1])]?.description }}</p>
                    <p> Day: {{getDayName(day)}}</p>
                    <p> Time: {{ weather_hourly.time[iterate].split('T')[1]}}</p>
                    <p> Temperature: {{ weather_hourly.temp[iterate]}}{{ weatherunits.temp }}</p>
                    <p> Feels like: {{ weather_hourly.apparent_temperature[iterate]}}{{ weatherunits.temp }}</p>
                    <p> Humidity: {{ weather_hourly.humidity[iterate]}}{{ weatherunits.humidity }}</p>
                    <p> Precipitation: {{ weather_hourly.precipitation_probability[iterate]}}{{ weatherunits.precipitation_probability }}</p>
                    <p> Wind Speed: {{ weather_hourly.wind[iterate]}}{{ weatherunits.wind }}</p>
                </div>
           </div>
           
      </div>


      <!--80-->
    </div>

  </div>
  </div>
</template>
<style scoped></style>
