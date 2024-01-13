<template>
    <div>
        <button @click="toggleForm"><slot /></button>
        <AddCommentForm v-if="formVisible" />
    </div>  
</template>

<script>
import AddCommentForm from './AddCommentForm.vue';
export default {
    name: 'BtnFormCreate',
    components : {
        AddCommentForm
    },
    data() {
        return {
            formVisible: false
        }
    },
    methods: {
        toggleForm() {
            if(!this.formVisible) {
                this.formVisible = true
                
            } else {
                this.formVisible = false       
            }
        }
    }
}
</script>

<style lang="scss">




</style>