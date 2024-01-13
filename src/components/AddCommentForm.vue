<template>
    <div class="comment__popup" id="comment__popup" aria-modal="true" role="dialog">
        <form @submit.prevent='addComment' class="add-comment" @keydown.esc="closeForm">
            <button type="button" class="add-comment__close" @click="closeForm">X</button>
            <label class="add-comment__label " for="add-comment__author">Имя</label>
            <input type="name" v-model="author" id="add-comment__author" class='add-comment__author' placeholder="Ваше имя" required>
            <label class="add-comment__label" for="add-comment__text">Комментарий</label>
            <textarea class='add-comment__text' id="add-comment__text" wrap="hard"  v-model="text" placeholder="Ваш комментарий" required ></textarea>
            <fieldset class="reaction">
                <legend class="reaction__legend">Реакция</legend>
                <input class="reaction__input" type="radio" name="reaction" :id="`negative-reaction-${parent}`" value="-1"   v-model="reaction">
                <label class="reaction__label-negative " :for="`negative-reaction-${parent}`"><IconNegativeReaction  alt="negative" /></label>
                <input class="reaction__input"  type="radio" name="reaction" :id="`neutral-reaction-${parent}`" value="0"  v-model="reaction" >   
                <label class="reaction__label-neutral" :for="`neutral-reaction-${parent}`"><IconNeutralReaction alt="neutral"/></label>
                <input class="reaction__input" type="radio" name="reaction" :id="`positive-reaction-${parent}`" value="1"  v-model="reaction" >  
                <label class="reaction__label-positive" :for="`positive-reaction-${parent}`"><IconPositiveReaction  alt="positive" /> </label>  
            </fieldset>
            <button class="add-comment__submit" :class="{'disabled': isDisabled}" :disabled="isDisabled">Отправить</button>
        </form>
    </div> 
</template>


<script>
import IconNegativeReaction from './icons/IconNegativeReaction.vue'
import IconNeutralReaction from './icons/IconNeutralReaction.vue'
import IconPositiveReaction from './icons/IconPositiveReaction.vue'
export default {
    name: 'AddCommentForm',
    components: {
        IconNegativeReaction,
        IconNeutralReaction,
        IconPositiveReaction
    },
    props: {
        parent: {
            default: null,
            type: Number,
        }
    } ,
    data() {
        return {
            author:'',
            text:'',
            reaction: 0,
            isDisabled: false,
        }
        
    },
    methods: {
        async addComment() {
            try {
                this.isDisabled = true
                const data = {
                    author: this.author,
                    text: this.text,
                    rection: this.reaction,
                    parentId: this.parent,
                }
                await fetch('http://194.67.93.117:80/comments', {
                    method: 'POST',
                    headers: {
                        'Username': 'KirAuki',
                        'Accept': 'application/json',
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(data)
                });
            } catch (error){
                console.error(error)
            } finally {
                this.isDisabled = false
                this.$parent.formVisible = false
                this.author = ''
                this.text = ''
                this.reaction = 0
                document.body.classList.remove('modal-open');
            }
            
        },
        closeForm() {
            this.$parent.formVisible = false
            document.body.classList.remove('modal-open');
        }
    }
    
}
</script>

<style lang="scss">
.comment__popup {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    z-index: 9999;
}
.add-comment{
    background-color: rgb(20, 20, 20);
    width: clamp(350px, 40vw, 800px);
    height: clamp(350px, 50vh, 500px);
    border-radius: 10px ;
    font-family: inherit ;
    font-weight: inherit;
    margin: 0 auto;
    color: inherit;
    padding: 10px;
    display: flex;
    font-size: clamp(0.8rem,1.5vw,1.3rem);
    flex-direction: column;
    &__label {
        max-width: 200px;
        margin: 10px auto;
        text-align: center;
    }
    &__close {
        width: 32px;
        height: 32px;
        float: right;
        margin-bottom: 20px ;
        cursor: pointer;
        &:hover, &:focus {
            background-color: red ;
        }
    }
    &__author {
        min-height: 40px;
        width: clamp(300px, 30vw, 400px);
        font-size: clamp(1.2rem,1.5vw,1.6rem);
        background-color: rgb(20, 20, 20) ;
        color: inherit;
        border: 0;
        border-bottom: 2px solid rgb(161, 161, 161);
        margin: 0 auto;
    }
    &__text {
        resize: none;
        border-radius: 10px ;
        overflow: hidden;
        padding-bottom: 2px;
        padding-top: 2px;
        font-size: clamp(1.2rem,1.5vw,1.6rem);
        position: relative;
        white-space: pre-wrap;
        width: clamp(300px, 30vw, 400px);
        height: clamp(40px, 10vh, 400px) ;
        color: inherit;
        background-color: rgb(20, 20, 20) ;
        margin: 0 auto;
        @media (width >= 801px) {
          
        }
        @media (width >= 2000px) {
        }
    }
    &__submit {
        border-radius: 5px;
        &:hover, &:focus {
            background-color: #51c951 ;
        }
    }
}

span {
    max-width: 150px;
}
.reaction {
    display: flex;
    justify-content: space-between;
    gap: 20px;
    align-items: center;
    margin: 30px  auto;
    width: clamp(100px, 30vw, 200px);
    &__legend {
        margin-bottom: 10px;
    }
    &__label {
        cursor: pointer;
        &-negative:hover {
            color: #F55C5C;
        }
        &-neutral:hover {
            color: rgb(161, 161, 161);
        }
        &-positive:hover {
            color: #52CC52
        }
        
    };
    & label {
        cursor: pointer;
        & svg {
        width: clamp(10px, 10vw, 40px);
        height: clamp(10px, 10vw, 40px);
    }
    }
    &__input {
        display: none;
        margin: 0 5px;
        &:checked + .reaction__label{
            &-negative svg  {
                color: #F55C5C;
            }
            &-neutral svg {
                color: rgb(161, 161, 161)
            } 
            &-positive svg {
                color: #52CC52
            }
    }
    }
    
}
</style>