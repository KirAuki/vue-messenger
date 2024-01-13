<template>
    <li class="comment">
        <div class="comment__content" :class="{'comment-positive': reactionSum(comment) > 0 , 'comment-negative': reactionSum(comment) < 0 , 'comment-neutral': reactionSum(comment) === 0}">
            <p class="comment__author">{{ comment.author }}</p>
            <p class="comment__text">{{ comment.text }}</p>
            <div class="comment__info">
                <button class="comment__show-form" @click="showReplyForm(comment.id)"><a class="form-link" href="#comment__popup" tabindex="-1">Ответить</a></button>
                <p v-if="comment.childs.length > 0" class="comment__reply">Ответов: {{ comment.childs.length  }}</p>
                <time class="comment__time" >{{ moscowTime }}</time>
            </div>      
        </div>
        <ul class="comment__replies">
                <CommentItem  v-for="child in comment.childs" :key="child.id" :comment="child" @showForm="showReplyForm"/>
        </ul>
    </li>
</template>

<script>
export default {
    name: 'CommentItem',
    data () {
        return {
            moscowTime: ''
        }
    },
    mounted() {
        this.convertToMoscowTime();
    },
    methods: {
        showReplyForm(id) {
            this.$emit('showForm',id)
        },
        convertToMoscowTime() {
            let date = new Date(this.comment.createdAt);
            let padNumber = (number) => {
                return number.toString().padStart(2, '0');
            };
            let year = date.getFullYear();
            let month = padNumber(date.getMonth() + 1);
            let day = padNumber(date.getDate());
            let hours = padNumber(date.getHours());
            let minutes = padNumber(date.getMinutes());
            let seconds = padNumber(date.getSeconds());
            this.moscowTime = `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
        },
        reactionSum(comment) {
            let sum = 0;
            comment.childs.forEach(child => {
                sum += child.reaction;
            });
            return sum
        }
    },
    props: {
        comment: {
            type: Object,
            required: true
        },
    },
};
</script>

<style lang="scss">
.comment {
    padding-left: 10px;
    font-size: clamp(0.8rem,1.5vw,1.3rem);
    @media (width >= 801px) {
        padding-left: 15px;
    }
    @media (width >= 2000px) {
        padding-left: 20px;
    }
    &__content {
        margin: clamp(10px,2.5vw,30px) 0 ;
        padding: 5px;
        width: clamp(350px,40vw,1000px);
        word-break: break-word;
        border-radius: 5px;
    }
    &__author {
        margin: 10px;
        font-weight: bold;
        
        padding-bottom: 5px;
        color: rgb(255, 255, 255);
        border-bottom:1px solid rgb(51, 54, 57) ;
    }
    &__text {
        margin: 20px 10px;
        font-size: clamp(1.2rem,1.5vw,1.6rem);
    }
    &__info {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
        gap: 20px;
        margin: 10px;
    }
    &__replies {
        border-left: 2px solid rgb(51, 54, 57);
    }
    &-positive {
        background-color: #1f681f;
    }
    &-negative {
        background-color: #742222;
    }
    &-neutral {
        background-color: rgb(20, 20, 20);
    }
}
</style>
