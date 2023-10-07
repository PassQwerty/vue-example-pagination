<template>
    <div class="dialog" v-if="show === true" @click="hideDialog">
        <div @click.stop class="dialog_Content">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: 'my-dialog',
    props:{
        show: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        hideDialog(){
            this.$emit('update:show', false)
        }
    }
}
</script>

<style scoped>
.dialog{
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.5);
    position: fixed;

    display: flex;
}

.dialog_Content{
    margin: auto;
    padding: 1rem;
    background: white;
    border-radius: 8px;
    min-height: 10rem;
    min-width: 22rem;
}

</style>
