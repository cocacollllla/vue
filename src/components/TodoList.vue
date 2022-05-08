<template>
  <div>
    <div v-if="!todos.length">추가된 Todo가 없습니다</div>
    <div v-for="(todo, index) in todos" :key="todo.id" class="card">
      <div class="card-body" @click="moveTo(todo.id)">
        <!-- <input type="checkbox" v-model="todo.checked" /> -->
        <!--
          todo.checked는 props로 받은 todos의 각각의 데이터.
          props로 받았기 때문에 이 컴포넌트에서는 props로 받은
          데이터를 변경할 수 없음.
          근데 v-model로 todo.checked를 변경하고 있음.
          이러면 안됨. v-model은 양방향이라서. props는 단방향.
          그래서 밑처럼 수정함.

          v-model 대신 단방향인 v-bind를 사용함.
          input태그라서 v-bind:checked 로 사용. v-bind는 : 으로 대체 가능해서
          :checked 

          props로 받은 todos는 App.vue 에서 관리하고있음.
          그래서 여기서 변경할수가 없다
          근데 todos안의 checked를 변경해야하기 때문에
          v-model은 v-bind로 변경, @change로 체크박스에 변화가 생기면
          toggleTodo 라는 함수가 실행되게 하고
          그 함수 내에는 컨텍스트로 이 컴포넌트(자식) 에서 부모컴포넌트인 App.vue로
          해당 데이터의 index를 보내서 App.vue에서 그 인덱스를 활용해
          데이터를 변경할 수 있도록 한것.
        --> 
        <input type="checkbox" :checked="todo.checked" @change="toggleTodo(index, $event)" @click.stop /> 
        <!-- 
          @change="toggleTodo(index) 얘도 이벤트버블링으로 인해 동작이 안됨. 그래서 이벤트전파를 막아줘야하는데
          그래서 @change.stop 해주면 제대로 되지않음. 그래서 @click.stop 뒤에 붙여줌.
        -->
        <label :class="{ todoChecked: todo.checked}">
          <!-- 
            클래스 바인딩. 
            https://kr.vuejs.org/v2/guide/class-and-style.html 
            <div v-bind:class="{ active: isActive }"></div>
            v-bind:class   =>  :class 
            (v-bind 생략가능해서 :class로만 작성.)
            :class="{ active: isActive }"
            active는 클래스 이름.
            isActive는 true, false

            <label :class="{ todoChecked: todo.checked}">
            todoChecked 는 클래스 이름.
            todo.checked는 true, false
            todo.checked가 true일 때 todoChecked 클래스가 생길것.
          -->
          {{ todo.subject }}
          <span @click.stop="todoDelete(index)">삭제</span>
          <!--
            이벤트버블링으로 인해 삭제버튼을 클릭해도 상세페이지로 이동됨.
            그래서 이벤트전파를 막아줘야함.
            click.stop 이렇게 작성하여 이벤트전파를 막음.
          -->
        </label>
      </div>
    </div>
    <!--
      react 
      {todos.map(el => (
        <div key={el.id}>{el.subject}</div>
      ))}
      -->
  </div>
</template>

<script>
import {watchEffect} from 'vue';
import {useRouter} from 'vue-router';
export default {
  props: ['todos'], // props를 받을 때
  emits: ['toggle-todo', 'delete-todo'], 
  // vue3 부터는 context를 사용했을때 emits를 써줘야한다.
  // 사용한 컨텍스트 이름을 모두 배열 안에 넣어준다.

  setup (props, context) {

    const router = useRouter();

    watchEffect(() => {
      console.log(props.todos.length); // 맨처음엔 초기값 0 이 뜨고 나중에 axios로 데이터를 받아와 업데이트된 후에 5로 변경됨
    });

    const toggleTodo = (index, event) => {
      context.emit('toggle-todo', index, event.target.checked);
    }

    const todoDelete = (index) => {
      context.emit('delete-todo', index);
    }
  
    const moveTo = (id) => {
      // router.push(`/todos/${id}`);
      router.push({
        name: 'Todo',
        params: {
          id: id
        }
      })
    }

    return {
      toggleTodo,
      todoDelete,
      moveTo
    }
  }

  // 구조분해할당을 사용하여 context.emit이 아닌 그냥 emit으로만 작성할 수 있도록
  //   setup (props, { emit }) {
  //   const toggleTodo = (index) => {
  //     emit('toggle-todo', index);
  //   }

  //   const todoDelete = (index) => {
  //     emit('delete-todo', index);
  //   }
  
  //   return {
  //     toggleTodo,
  //     todoDelete
  //   }
  // }
}
</script>

<style>

</style>
