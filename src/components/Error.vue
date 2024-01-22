<script setup>
defineProps({
    msg: String
})
</script>

<template>
    <div class="error">{{ msg }}</div>
</template>

<style scoped>
div{
    margin-bottom: 20px;
    color: #fff;
    padding: .6rem 0;
    border-radius: 8px;
}
</style>