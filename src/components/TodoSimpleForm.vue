<template>
  <div>
    <form @submit.prevent="onSubmit">
      <div>
        <input type="text" placeholder="new To Do List" v-model="todo" />
      </div>
      <div>
        <button class="btn" type="submit">Add</button>
      </div>
    </form>
    <div class="error" v-if="hasError">할일을 입력해주세요.</div>
  </div>
</template>
  
<script>
  import { ref } from 'vue';
  export default {
    emits: ['add-todo'],
    setup(props, context) { // context 자식컴포넌트에서 부모컴포넌트로 데이터 보낼때
      const todo = ref('');

      const hasError = ref(false);

      const onSubmit = () => {
        // e.preventDefault();  위에서 @submit.prevent 해주면 여기서 e.preventDefault() 해줄 필요 없음
        // https://vuejs.org/guide/essentials/template-syntax.html#modifiers

        if(todo.value === '') {
          hasError.value = true;
        } else {
          context.emit('add-todo', { // context.emit('이벤트이름', 보낼데이터)
            id: Date.now(),  // 여기서는 이벤트이름이 add-todo, 보낼데이터는 id, subject, checked가 있는 객체
            subject: todo.value,
            checked: false
          });
          // todos.value.push({
          //   id: Date.now(),
          //   subject: todo.value,
          //   checked: true
          // });
          hasError.value = false;
          todo.value = '';
        }
        
        

      }

      return {
        todo,
        onSubmit,
        hasError
      }
    }
  }
</script>

<style></style>