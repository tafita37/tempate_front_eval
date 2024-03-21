<script setup>
    const props=defineProps(["submitMessage", "submitFunction"]);
</script>
<template>
    <button type="submit" class="btn btn-primary mr-2">
        {{ props.submitMessage }}
    </button>
</template>
<style scoped>
    @import "@/assets/css/reeuse/submitButton.css";
</style>