<template>
    <div class="location-forecast">
        <img 
            class="location-forecast_icon"
            :src="`http://openweathermap.org/img/wn/${forecast.current.weather[0].icon}@2x.png`"
        />
        <h2 class="location-forecast_title">{{ location.name }} {{ Math.round(forecast.current.temp) }}°C</h2>
        <p>Humidity: {{ forecast.current.humidity }}%</p>
        <p>Wind Speed: {{ forecast.current.wind_speed }}m/s</p>
        <p>Cloud Cover: {{ forecast.current.clouds }}%</p>
        <div class="chart-container">
            <canvas ref="chart"></canvas>
        </div>
        <div class="week-forecast">
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
        location: Object,
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
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        display: false,
                    },
                    tooltip: {
                        intersect: false,
                        displayColors: false,
                        backgroundColor: 'rgba(0, 0, 0, 0)',
                        bodyColor: '#000000',
                        yAlign: 'bottom',
                        callbacks: {
                            title: () => false,
                            label: (context) => context.parsed.y.toFixed(1),
                        },
                    },
                },
                scales: {
                    y: {
                        suggestedMin: Math.floor(Math.min(...this.chartData)) - 1,
                        suggestedMax: Math.ceil(Math.max(...this.chartData)) + 1,
                    },
                    x: {
                        ticks: {
                            maxRotation: 0,
                        },
                    },
                },
            },
        });
    },
    computed: {
        chartLabels: function () {
            return this.forecast.hourly
                .slice(0, 24)
                .map(hour => (new Date(hour.dt * 1000)))
                .map(datetime => datetime.toTimeString().substring(0, 5));
        },
        chartData: function () {
            return this.forecast.hourly.slice(0, 24).map(hour => hour.temp);
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

<style lang="scss">
.location-forecast {
    border: 1px solid black;
    border-radius: 0.5rem;
    margin-top: 1rem;
    padding: 1rem;

    &_icon {
        float: left;
        border-radius: 50%;
        background-color: #acc1ff;
        margin-right: 1rem;
    }

    &_title {
        margin-bottom: 0.5rem;
    }
}

.chart-container {
    position: relative;
    width: 100%;
    height: 200px;
}

.week-forecast {
    text-align: center;
}
</style>
