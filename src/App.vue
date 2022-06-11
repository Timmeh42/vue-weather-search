<template>
    <div id="app">
        <h1 class="app_title">OpenWeather API Search & Forecast</h1>
        <form
            class="search-form"
            @submit.prevent="searchLocation(locationName)"
        >
            <input
                class="search-form_input"
                type="search"
                v-model="locationName"
                placeholder="Search by place name"
            >
            <button class="search-form_button">Search</button>
        </form>
        <div v-if="locationResults.length">
            <location-result
                v-for="location of locationResults"
                :key="`${location.lat},${location.lon}`"
                :location="location"
                @loadForecast="loadForecast"
            />
        </div>
        <location-forecast
            v-if="forecast"
            :forecast="forecast"
            :location="locationResults[0]"
        />
    </div>
</template>

<script>
import { countries } from 'country-data';
import LocationForecast from './components/LocationForecast.vue';
import LocationResult from './components/LocationResult.vue';

export default {
    name: 'App',
    components: {
        LocationForecast,
        LocationResult,
    },
    data: function () {
        return {
            locationName: '',
            locationResults: [],
            forecast: undefined,
            countries,
        }
    },
    methods: {
        searchLocation: function (locationName) {
            fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${locationName}&limit=3&appid=${process.env.VUE_APP_API_KEY}`)
                .then((response) => response.json())
                .then((searchResults) => {
                    this.locationResults = searchResults;
                });
        },
        loadForecast: function ({lat, lon}) {
            this.locationResults = this.locationResults.filter(location => location.lon === lon && location.lat === lat);
            fetch(`http://openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&units=metric&appid=${process.env.VUE_APP_ONECALL_KEY}`)
                .then((response) => response.json())
                .then((forecastResult) => {
                    this.forecast = forecastResult;
                });
        }
    }
}
</script>

<style lang="scss">
#app {
    max-width: 44rem;
    margin: auto;
    padding-top: 4rem;
    font-family: Arial, Helvetica, sans-serif;
}

.app_title {
    text-align: center;
}

.search-form {
    padding: 1rem 0;
    text-align: center;

    &_input {
        border-radius: 0.3rem 0 0 0.3rem;
        border: 1px solid black;
        border-right-width: 0;
        padding: 0.125rem 0.25rem;
    }

    &_button {
        border-radius: 0 0.3rem 0.3rem 0;
        border: 1px solid black;
        padding: 0.125rem 0.25rem;
    }
}
</style>
