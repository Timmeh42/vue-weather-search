<template>
    <div id="app">
        <form @submit.prevent="searchLocation(locationName)">
            <input type="search" v-model="locationName">
            <button>Search</button>
        </form>
        <table v-if="locationResults.length">
            <thead>
                <tr>
                    <th>Flag</th>
                    <th>Place</th>
                    <th>State</th>
                    <th>Country</th>
                </tr>
            </thead>
            <tbody>
                <location-result
                    v-for="location of locationResults"
                    :key="`${location.lat},${location.lon}`"
                    :location="location"
                    @loadForecast="loadForecast"
                />
            </tbody>
        </table>
        <location-forecast v-if="forecast" :forecast="forecast" />
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

<style>
</style>
