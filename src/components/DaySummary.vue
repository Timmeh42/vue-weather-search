<template>
    <div>
        <h3>{{ summary.label }}</h3>
        <img :src="summary.icon" />
        <p>{{ summary.max }}° {{ summary.min }}°</p>
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

<style scoped>
</style>
