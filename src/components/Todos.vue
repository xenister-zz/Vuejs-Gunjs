<template>
    <div >
        <div v-bind:key="todo.id" v-for="todo in todos">
            <TodoItem v-bind:todo="todo" 
                v-on:del-todo="$emit('del-todo', todo.id)"
                v-on:mark-completed="$emit('mark-completed', todo.id, todo.completed)"
            />
        </div>
    </div>
</template>

<script>
import TodoItem from "./TodoItem.vue";
import Gun from 'gun'
import unset from 'gun/lib/unset'

var gun = Gun()

export default {
    name: "Todos",
    components: {
        TodoItem
    },
    props: ["todos"],
}


</script>

<style scoped>

</style>