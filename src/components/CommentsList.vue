<template>
    <div class="comments">
        <button class="comments__show-form" @click="showForm(null)"><a class="form-link" href="#comment__popup" tabindex="-1">Комментировать</a></button>
        <h2 class="comments__title">Количество комментариев: {{ commentsTree.length }}</h2>
        <div class="stream">
            <label class="stream__label" for="stream__input">Стрим комментариев</label>
            <input id="stream__input" type="checkbox" v-model="commentsStreamON" @change="commentsStream"/>
        </div>
        <ul class="comments__list">
            <CommentItem v-for="comment in commentsTree" :key="comment.id" :comment="comment" @showForm="showForm"/>
        </ul>
        <AddCommentForm v-if="formVisible" :parent="parentId"/>
    </div>
</template>


<style lang="scss">
.comments {
    &__title {
        margin: 20px auto;
        width: clamp(350px, 40vw, 500px);
        font-size: clamp(1.4rem,1.5vw,2.3rem);
    }
}
.stream {
    display: flex;
    flex-direction: row;
    width: clamp(350px, 40vw, 500px);
    margin: 0 auto;
    gap: 20px;
    &__label {
        font-size: clamp(1.2rem,1.5vw,2rem);
    }
}
.form-link {
    color: inherit;
}
</style>


<script>
import CommentItem from './CommentItem.vue';
import AddCommentForm from './AddCommentForm.vue';
export default {
    name: 'CommentsList',
    components: {
        CommentItem,
        AddCommentForm
    },
    data() {
        return {
            comments: [],
            commentsTree: [],
            commentsStreamON: true,
            eventSource: null,
            formVisible: false,
            parentId: null,
        };
    },
    async created() {
        await this.fetchComments();
        this.commentsStream()
    },
    methods: {
        async fetchComments() {
            try {
                const response = await fetch('http://194.67.93.117:80/comments');
                if (!response.ok) {
                throw new Error('Ошибка при получении комментариев');
            }
                const data = await response.json();
                this.comments = data.sort((a, b) => a.id - b.id);
                this.groupedComments();
            } catch (error) {
                console.error('Ошибка при получении комментариев:', error);
            } 
        },
        commentsStream() {
            if (this.commentsStreamON) {
                this.eventSource = new EventSource('http://194.67.93.117:80/comments/stream');
                this.eventSource.onmessage = async (event) => {
                    const newComment = JSON.parse(event.data);
                    this.comments.push(newComment);
                    this.groupedComments();
                };
            } else {
                this.eventSource.close();
            } 
        },
        showForm(id) {
            this.parentId = id
            this.formVisible = true
            document.body.classList.add('modal-open');
        },
        groupedComments() {
            
            const commentsMap = {}; 
            const rootComments = [];
    
            this.comments.forEach(comment => {
                const { id,author, text, reaction,createdAt, parentId } = comment;
                if (!commentsMap[id]) {
                    commentsMap[id] = { id,author, text, reaction,createdAt, parentId, childs: [] };
                    // создается объект коммента если его нет в списке
                }
                if (parentId === null) {
                    rootComments.push(commentsMap[id]);// добавленик в род. комменты если нет parentId
                } else {
                    if (!commentsMap[parentId]) {
                        commentsMap[parentId] = { id: parentId,author, text, reaction,createdAt, childs: [] };// Создается объект род. коммента
                    }
                    commentsMap[parentId].childs.push(commentsMap[id]);// Добавляется доч. комментарий в массив доч. комментов род.
                }
            });
    
            this.commentsTree = rootComments
        },
    },
    
};
</script>
