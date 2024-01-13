<script setup>
import CommentsList  from './components/CommentsList.vue'
</script>

<template>
    <main > 
        <CommentsList />
    </main>  
</template>

<style lang="scss">
#app {  
    margin: 0 auto;
    max-width: 1440px;
}
.modal-open {
    overflow-y: hidden ;
}
button {
    border-radius: 5px;
    background-color: rgb(29, 155, 240);
    border: 1px solid rgba(0, 0, 0, 0) ;
    color: inherit;
    cursor: pointer;
    justify-self: start;
    font-size: clamp(0.8rem,1.2vw,1.4rem) ;
    border-radius: 10px ;
    &:hover , &:focus {
        background-color: rgb(179, 178, 178) ;
    }
}
.disabled {
    background-color: black;
    color: white;
    opacity: 50%;
    &:focus, &:hover {
        background-color: black;
    }
}
</style>
