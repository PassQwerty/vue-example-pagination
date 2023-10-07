<template>
    <div class="post">
        <div>
            <div><strong>id: </strong>{{ post.id }}</div>
            <div><strong>Название: </strong>{{ post.title }}</div>
            <div><strong>Описание: </strong>{{ post.body }}</div>
        </div>
        <div class="postButtons">
            <my-button 
                    class="danger btnSizeM" 
                    style="align-items: flex-end;"
                    @click="deletePost">
                Удалить
            </my-button>
        </div>
    </div>
</template>

<script>

export default {
    props: {
        post: {
            type: Object,
            required: true,
        },
    },
    methods: {
        deletePost() {
            this.$emit("remove", this.post);
        }
    }
};
</script>

<style>
.post {
    padding: 15px;
    border: 2px solid var(--green);

    display: flex;
    justify-content: space-between;
    gap: 0.5rem;
}
</style>