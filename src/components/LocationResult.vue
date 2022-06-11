<template>
    <button class="location-result" @click="$emit('loadForecast', {lat: location.lat, lon: location.lon})">
        <span class="location-result_flag">
            {{ countries[location.country].emoji }}
        </span>
        <span class="location-result_text">
            {{ location.name }}, {{ location.state }}, {{ countries[location.country].name }}
        </span>
    </button>
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

<style lang="scss">
.location-result {
    display: block;
    padding: 0.5rem 1rem;
    width: 100%;
    text-align: start;

    border: 1px solid black;
    border-radius: 0;
    border-bottom-width: 0;

    &:first-child {
        border-top-left-radius: 0.5rem;
        border-top-right-radius: 0.5rem;
    }
    &:last-child {
        border-bottom-width: 1px;
        border-bottom-left-radius: 0.5rem;
        border-bottom-right-radius: 0.5rem;
    }

    &_flag {
        font-size: 200%;
        vertical-align: middle;
    }

    &_text {
        padding-left: 0.5rem;
        vertical-align: middle;
    }
}
</style>
