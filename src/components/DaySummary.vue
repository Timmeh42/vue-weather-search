<template>
    <div class="day-summary">
        <h3 class="day-summary_label">{{ summary.label }}</h3>
        <img class="day-summary_icon" :src="summary.icon" />
        <p class="day-summary_temp">{{ summary.max }}° {{ summary.min }}°</p>
    </div>
</template>

<script>
export default {
    name: 'DaySummary',
    props: {
        summary: Object,
    },
}
</script>

<style lang="scss">
.day-summary {
    display: inline-block;
    padding: 0.5rem;
    width: 5rem;
    text-align: center;

    &_label {
        margin: 0.25rem 0;
    }

    &_icon {
        background: #acc1ff;
        border-radius: 50%;
    }

    &_temp {
        margin: 0.25rem 0;
    }
}
</style>
