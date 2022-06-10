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
                <tr
                    v-for="location of locationResults"
                    :key="`${location.lon},${location.lat}`"
                >
                    <td>{{ countries[location.country].emoji }}</td>
                    <td><button @click="loadForecast({lat: location.lat, lon: location.lon})">{{ location.name }}</button></td>
                    <td>{{ location.state }}</td>
                    <td>{{ countries[location.country].name }}</td>
                </tr>
            </tbody>
        </table>
        <div v-if="forecastResult">
            <img :src="`http://openweathermap.org/img/wn/${forecastResult.current.weather[0].icon}@2x.png`" />
            Temperature: {{ forecastResult.current.main.temp }}c
            Humidity: {{ forecastResult.current.main.humidity }}%
            Wind Speed: {{ forecastResult.current.wind.speed }}m/s
            Cloud Cover: {{ forecastResult.current.clouds.all }}%
        </div>
    </div>
</template>

<script>
import { countries } from 'country-data';

export default {
    name: 'App',
    data: function () {
        return {
            locationName: '',
            locationResults: [],
            forecastResult: undefined,
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
        loadForecast: function ({lon, lat}) {
            Promise.all([
                    fetch(`http://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&units=metric&appid=${process.env.VUE_APP_API_KEY}`).then((res) => res.json()),
                    fetch(`http://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&appid=${process.env.VUE_APP_API_KEY}`).then((res) => res.json()),
                ])
                .then((responses) => ({
                    forecast: responses[0],
                    current: responses[1],
                }))
                .then((res) => {
                    this.forecastResult = res;
                });
        }
    }
}
</script>

<style>
</style>
