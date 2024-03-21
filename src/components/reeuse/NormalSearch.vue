<script setup>
    const props=defineProps(["placeHolderSearch", "searchFunction", "reactiveObject", "reactiveReference"]);

    const handleSubmit = () => {
        props.searchFunction();
    };
</script>
<template>
    <ul class="navbar-nav mr-lg-2">
        <li class="nav-item nav-search d-none d-lg-block">
            <div class="input-group">
                <div class="input-group-prepend hover-cursor" id="navbar-search-icon">
                    <span class="input-group-text" id="search">
                        <i class="icon-search"></i>
                    </span>
                </div>
                <form @submit.prevent="handleSubmit">
                    <input type="text" class="form-control" id="navbar-search-input" :placeholder="props.placeHolderSearch" 
                    v-model="props.reactiveObject[props.reactiveReference]">
                </form>
            </div>
        </li>
    </ul>
</template>
<style scoped>
    @import "@/assets/css/reeuse/normalSearch.css";
    @import "@/assets/css/feather.css";
</style>