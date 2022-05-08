<template>
  <div v-if="loading">Loading...</div>
  <form v-else @submit.prevent="onSave">
    <div class="formBox">
      <div>
        <div class="todoSubject">Subject</div>
        <input type="text" v-model="todo.subject" />
      </div>
      <div v-if="editing">
        <div class="todoSubject">Complete</div>
        <input type="checkbox" v-model="todo.checked" @click="todoComplete()"  />
      </div>
      <div>
        <div class="todoSubject">Body</div>
        <textarea v-model="todo.body" cols="30" rows="10" ></textarea>
      </div>
    </div>
    
    <button>{{editing ? 'update' : 'create'}}</button>
    <button @click="moveToTodoListPage()">cancel</button>
  </form>
  <Toast v-if="showToast" :message="toastMessage" :type="toastAlertType" />
</template>

<script>
import { useRoute, useRouter } from 'vue-router';
// import { ref, onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted } from '@vue/reactivity';
import { ref } from '@vue/reactivity';

import axios from 'axios';
import Toast from '@/components/Toast.vue';
import { useToast } from '@/hooks/toast';

export default {
  components: {
    Toast
  },
  props: {
    editing: {
      type: Boolean,
      default: false,
      body: ''
    }
  },

 setup(props) {
  const route = useRoute();
   const router = useRouter();
   const todo = ref({
     subject: '',
     checked: false
   });
   const loading = ref(false);

   const { toastMessage, toastAlertType, showToast, triggerToast } = useToast();

  //  onBeforeMount(() => {
  //    // 마운트 되기 전 실행될 것
  //    console.log('dd');
  //  });
  //  onMounted(() => {
  //    // 마운트 됐을 때 
  //    console.log('dd');
  //  });
  //  onBeforeUpdate(() => {
  //   // state 업데이트되기 전에 실행될 것
  //   console.log('dd');
  //  });
  //  onUpdated(() => {
  //    // state가 업데이트될 때
  //    console.log('dd');
  //  });
  //  onBeforeUnmount(() => {
  //    // 언마운트 되기 전
  //    console.log('dd');
  //  });
  //  onUnmounted(() => {
  //    // 언마운트 한 후 실행될 것
  //    // 메모리누수방지를 위해 활용될 때가 많음.
  //    // 메모리누수란 만약 컴포넌트에서 어떤 버튼을 클릭하면
  //    // setTimeOut을 사용해 3초 후 콘솔을 나타내게 할 경우
  //    // 버튼 클릭후 3초가 지나기 전에 다른컴포넌트로 이동하면
  //    // setTimeOut이 사라지지않고 그대로 진행되어 다른컴포넌트로 이동해도
  //    // 3초 후 콘솔이 나타나게 된다.
  //    // 이미 다른컴포넌트로 이동했기 때문에 쓸모없는 동작이므로 메모리 누수라고 볼 수 있다
  //    // 이런 현상을 방지하기 위해 컴포넌트가 언마운트될 때 이런 것들을 정리하기 위해 사용한다

  //   clearTimeout(timeout.value); // 언마운트될 때 setTimeOut 멈추게하기

  //  });


  const getTodo = async () => {
    try {
      const res = await axios.get(`http://localhost:3000/todos/${route.params.id}`);
      todo.value = res.data;
      loading.value = false;
    } catch(err) {
      console.log(err);
      loading.value = false;
      triggerToast('Something went wrong', 'danger');
    }
    
  }

  const todoComplete = () => {
    todo.value.checked = !todo.value.checked
  }

  

  const moveToTodoListPage = () => {
    router.push({
      name: 'Todos'
    })
  }

  if(props.editing) {
    getTodo();
  }



  const onSave = async () => {

    try {
      const data = {
        subject: todo.value.subject,
        checked: todo.value.checked,
        body: todo.value.body
      }
      if(props.editing) {
        await axios.put(`http://localhost:3000/todos/${route.params.id}`, data);
      } else {
        await axios.post(`http://localhost:3000/todos`, data);
      }
      
      triggerToast('Successfully saved!');
    } catch(err) {
      console.log(err);
      triggerToast('Something went wrong', 'danger');
    }
    
  }

  return {
    todo,
    loading,
    todoComplete,
    moveToTodoListPage,
    onSave,
    showToast,
    toastMessage,
    toastAlertType,
    triggerToast
  }
 }
}
</script>

<style>
  .todopageTitle {
    font-size: 1.2rem;
    font-weight: 500;
  }

  .todoSubject {
    margin: 40px 0 20px 0;
  }

  form {
    display: block;
  }

  .formBox {
    display: flex;
    
  }

  .formBox>div:first-of-type {
    margin-right: 30px;
  }
</style>