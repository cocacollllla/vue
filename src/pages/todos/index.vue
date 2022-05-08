<template>
  <div class="container">
    <div>count: {{ count }}</div>
    <div>double count computed: {{ doubleCountComputed }}</div>
    <div>double count method: {{ doubleCountMethod() }}</div>
    <button @click="count++">버튼</button>
    
    <div class="titlebutton">
      <h2>To Do List</h2>
      <button @click="moveToCreatePage()">Create Todo</button>
    </div>
    
    <input type="text" placeholder="Search" v-model="searchText" class="search" />
    <hr />
    <!-- <TodoSimpleForm @add-todo="addTodo" /> -->
    <!-- TodoSimpleForm 컴포넌트에서 보낸데이터. @데이터이름="함수" -->

    <!-- 
    <div v-show="toggle">true</div>
    <div v-show="!toggle">false</div> 
  -->
    <!-- 
      <div v-if="toggle">true</div>
      <div v-else>false</div>
      
      <button @click="toggleClick">Toggle</button> 
    -->
    <TodoList :todos="todos" @toggle-todo="toggleTodo" @delete-todo="todoDelete" />
    <!-- 
      props 보내는법. :프롭스이름="보낼프롭스" 
      
      react
      <TodoList todos={todos} />
    -->
    <hr />
    <TodoPaginate @get-todos="getTodos" :currentPage="currentPage" :numberOfPages="numberOfPages" />
  </div>
  <Toast v-if="showToast" :message="toastMessage" :type="toastAlertType" />
</template>

<script>
  import { ref, reactive, computed, watch, watchEffect } from 'vue'; // ref 사용시 불러오기 string, number
  import {useRouter} from 'vue-router';
  // import TodoSimpleForm from '@/components/TodoSimpleForm.vue';
  import TodoList from '@/components/TodoList.vue';
  import TodoPaginate from '@/components/TodoPaginate.vue';
  import Toast from '@/components/Toast.vue';
  import { useToast } from '@/hooks/toast';
  import axios from 'axios';

  export default {
    components: {
      // TodoSimpleForm,
      TodoList,
      TodoPaginate,
      Toast
    },
    setup() {

      const { toastMessage, toastAlertType, showToast, triggerToast } = useToast();

      const router = useRouter();
      
      // =================== computed
      const count = ref(1);

      const doubleCountComputed = computed(() => {
        return count.value * 2
      });

      const doubleCountMethod = () => {
        return count.value * 2;
      }

      // computed와 그냥 함수의 차이점
      // 겉보기에는 그닥 차이점이 없어보인다. 이렇게 하면 동작도 똑같이 한다.
      // 1. computed는 인자를 받을 수 없음.
      //    함수는 인자를 받을 수 있음. 인자로 변수를 받아 count.value * name(인자로받은것) 가능
      //    하지만 computed는 이렇게 인자를 받아 계산할 수 없다.
      //    computed는 오직 state가 변경할때만 작동함.
      // 2. computed는 값을 저장함
      //    <div>double count computed: {{ doubleCountComputed }}</div>
      //    <div>double count method: {{ doubleCountMethod() }}</div>
      //    위에서 이렇게 작성했는데 만약 이렇게 한번이 아닌 두번씩 작성한다면 
      //    <div>double count computed: {{ doubleCountComputed }}</div>
      //    <div>double count computed: {{ doubleCountComputed }}</div>
      //    <div>double count method: {{ doubleCountMethod() }}</div>   
      //    <div>double count method: {{ doubleCountMethod() }}</div> 
      //    이렇게 두번씩 작성한다면
      //    함수는 두번 렌더링되는데 computed는 한번만 렌더링됨.
      //    함수는 값을 저장하지 않기때문에 첫번째줄에서 뭐가 리턴되든
      //    두번째줄의 함수도 또 실행되기 때문에 두번 실행한만큼 두번 렌더됨.
      //    근데 computed는 첫번째줄에서 실행한 값을 저장하고 기억해서
      //    두번째줄에서 다시 실행하는게 아니라 위에서 기억한 값을 출력할 뿐이라서
      //    한번만 렌더링됨. 
      //    두번씩 태그를 써보고 computed와 함수에 콘솔찍어보면
      //    computed는 한번 콘솔이 찍히고 함수는 두번 콘솔이 찍히는걸 볼 수 있다 
      // ==============================

      const todos = ref([]);

      const toggle = ref(false);

      const numberOfTodos = ref(0);
      const limit = 5;
      const currentPage = ref(1);

      const searchText = ref('');

      const toggleClick = () => {
        toggle.value = !toggle.value
      };

      const a = reactive({
        b: 1,
        c: 3
      });

      // reactive 하나 watch하는 법
      watch(() => a.b, (current, prev) => { 
        // ref가 아닌 reactive는 watch 문법이 조금 다름. reactive는 이렇게 사용함.
        console.log(current, prev);
        // 밑에서 a.b = 2 로 업데이트 해줬기 때문에 열자마자 업데이트되어 처음부터 업데이트된것처럼 보이지만 사실 처음엔 렌더가 안됐다가
        // 밑에서 업데이트되어 렌더된것임. 그래서 현재값인 2 , prev 값인 1 이 되어 console 찍어보면 2 1 이 나옴.
      });

      // reactive 여러개 watch하는 법
      watch(() => [a.b, a.c], (current, prev) => {
        // [a.b, a.c] 두개 모두 watch하고 있기 때문에 둘 중 하나라도 업데이트되면 console.log가 동작함. 
        console.log(current, prev);
      });

      a.b = 2;

      // ref 하나 watch하는 법
      watch(currentPage, (currentPage, prev) => { // 인자로 첫번째는 현재값, 두번째는 state의 전 값이 들어감.
        console.log(currentPage, prev);
        // watch와 watchEffect의 차이점
        // watch는 처음엔 렌더 안됨. 업데이트되어야 렌더됨. 그리고 하나의 state에 따라 함수를 줄 수 있음. 그 하나의 state가 변경될때 줄 이벤트를 함수로 만들 수 있ㅇㅁ
        // watchEffect는 처음에도 렌더 됨. 그리고 안에 여러 state가 있을때 그 중 하나의 state가 변경되면 안의 다른 state는 변경되지않아도 그 하나로 인해 또 렌더가 일어남 
      });

      // ref 여러개 watch하는 법
      watch([currentPage, numberOfTodos], (currentPage, prev) => {
        console.log(currentPage, prev);
      });

      watch(searchText, () => {
        getTodos(1)
      })

      watchEffect(() => {
        // react의 useEffect같은.
        // 맨처음에 한번 렌더되고 안의 ref, reactive들이 변경될때마다 업데이트됨. props, computed도 변경될때마다 업데이트됨.
        console.log(currentPage.value); // 맨처음엔 초기값 1 이었다가 axios로  데이터 받아오고 업데이트되어 4 로 나타나게 됨
      });

      const numberOfPages = computed(() => {
        return Math.ceil(numberOfTodos.value/limit);
      })

      const getTodos = async (page = currentPage.value) => {
        currentPage.value = page;
        try {
          const res = await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`);
          todos.value = res.data;
          numberOfTodos.value = res.headers['x-total-count'];
          triggerToast('Something went wrong', 'success');
        } catch (err) {
          console.log(err);
          triggerToast('Something went wrong', 'danger');
        }
      }
      getTodos();

      const addTodo = async (todo) => {
        // 위에서 자식컴포넌트에서 데이터를 받아오고 실행할 함수. 
        // 인자 todo는 받아온 데이터.
        try {
          await axios.post('http://localhost:3000/todos', {
            subject: todo.subject,
            checked: todo.checked
          });
          // todos.value.push(res.data);
          getTodos(1);
        } catch (err) {
          console.log(err);
          triggerToast('Something went wrong', 'danger');
        }
        
     
        // }).then(res => {
        //   todos.value.push(res.data);
        // }).catch(err => {
        //   console.log(err);
        // });
      }
    
      const toggleTodo = async (index, checked) => {
        const id = todos.value[index].id;
        try {
          await axios.patch(`http://localhost:3000/todos/${id}`, {
            checked : checked
          });
          todos.value[index].checked = checked
        } catch (err) {
          console.log(err);
          triggerToast('Something went wrong', 'danger');
        }
        
      }

      


      const todoDelete = async (index) => {
        const id = todos.value[index].id;
        try {
          await axios.delete(`http://localhost:3000/todos/${id}`);
          // todos.value.splice(index, 1);
          getTodos(1);
        } catch (err) {
          console.log(err);
          triggerToast('Something went wrong', 'danger');
        }
        
        // todos.value.filter(el => el.id !== id);
        
 
      }


      // const filteredTodos = computed(() => {
      //   if(searchText.value) {
      //     return todos.value.filter(todo => {
      //       return todo.subject.includes(searchText.value);
      //     })
      //   } else {
      //     return todos.value;
      //   }
      // })


      const moveToCreatePage = () => {
        router.push({
          name: 'TodoCreate'
        })
      }

      return {
        todos,
        toggle,
        toggleClick,
        addTodo,
        todoDelete,
        toggleTodo,
        count,
        doubleCountComputed,
        doubleCountMethod,
        searchText,
        // filteredTodos,
        numberOfPages,
        currentPage,
        getTodos,
        toastMessage,
    toastAlertType,
    showToast,
    triggerToast,
    moveToCreatePage
      }
    }
  }
</script>

<style>



  form {
    display: flex;
    justify-content: flex-start;
  }

  form div:first-of-type input {
    padding: 10px;
    border-radius: 10px;
    border: 1px solid #ddd;
  }
  .name {
    padding: 20px 30px;
    font-size: 20px;
  }

  .redName {
    color: salmon;
  }

  .btn {
    padding: 10px 20px;
    background-color: salmon;
    border-radius: 10px;
    border: 0;
    color:  white;
    font-weight: 500;
    margin-left: 5px;
  }

  .card-body {
    border: 1px solid #ddd;
    border-radius: 5px;
    padding: 10px;
    margin-top: 5px;
  }

  .error {
    color: red;
  }

  .card-body input {
    margin-right:8px
  }

  .todoChecked {
    text-decoration: line-through;
  }

  .search {
    padding: 10px;
    border-radius: 10px;
    border: 1px solid #ddd;
  }

  .titlebutton {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .titlebutton button {
    width: 100px;
    height: 30px;
    border-radius: 10px;
    border: 0;
    background-color: lemonchiffon;
  }
  
</style>
