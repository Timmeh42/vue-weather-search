<template>
    <tr>
        <td>{{ countries[location.country].emoji }}</td>
        <td><button @click="$emit('loadForecast', {lat: location.lat, lon: location.lon})">{{ location.name }}</button></td>
        <td>{{ location.state }}</td>
        <td>{{ countries[location.country].name }}</td>
    </tr>
</template>

<script>
import { countries } from 'country-data';

export default {
    name: 'LocationResult',
    props: {
        location: Object,
    },
    data: function () {
        return {
            countries,
        };
    },
}
</script>

<style scoped>
</style>
