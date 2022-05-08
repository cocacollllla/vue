<template>
  <div class="todopageTitle">Todo Page</div>
  <TodoForm :editing="true" />
</template>

<script>
  import TodoForm from '@/components/TodoForm.vue';
  export default {
    components: {
      TodoForm
    }
  }
</script>

<style>



</style>