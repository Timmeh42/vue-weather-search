<template>
    <div>
        <img :src="`http://openweathermap.org/img/wn/${forecast.current.weather[0].icon}@2x.png`" />
        Temperature: {{ forecast.current.temp }}c
        Humidity: {{ forecast.current.humidity }}%
        Wind Speed: {{ forecast.current.wind_speed }}m/s
        Cloud Cover: {{ forecast.current.clouds }}%
        <div class="chart-container">
            <canvas ref="chart"></canvas>
        </div>
        <div>
            <day-summary
                v-for="(day, i) of daySummaries"
                :key="i"
                :summary="day"
            />
        </div>
    </div>
</template>

<script>
import Chart from 'chart.js/auto';
import DaySummary from './DaySummary.vue';

export default {
  components: { DaySummary },
    name: 'LocationForecast',
    props: {
        forecast: Object,
    },
    mounted: function () {
        const ctx = this.$refs.chart.getContext('2d');
        new Chart(ctx, {
            type: 'line',
            data: {
                labels: this.chartLabels,
                datasets: [{
                    label: 'temperature',
                    data: this.chartData,
                    borderWidth: 1,
                    borderColor: '#f6cc2a',
                    fill: true,
                    backgroundColor: '#fdf5d4',
                    pointRadius: 0,
                }],
            },
            options: {
                scales: {
                    y: {
                        suggestedMin: Math.floor(Math.min(...this.chartData)) - 1,
                        suggestedMax: Math.ceil(Math.max(...this.chartData)) + 1,
                    },
                },
            },
        });
    },
    computed: {
        chartLabels: function () {
            return this.forecast.hourly
                .map(hour => (new Date(hour.dt * 1000)))
                .map(datetime => datetime.toTimeString().substring(0, 5));
        },
        chartData: function () {
            return this.forecast.hourly.map(hour => hour.temp);
        },
        daySummaries: function () {
            return this.forecast.daily.map(day => ({
                min: Math.round(day.temp.min),
                max: Math.round(day.temp.max),
                label: new Date(day.dt * 1000).toString().substring(0, 3),
                icon: `http://openweathermap.org/img/wn/${day.weather[0].icon}.png`,
            }));
        }
    },
}
</script>

<style scoped>
.chart-container {
    width: 400px;
}
</style>
