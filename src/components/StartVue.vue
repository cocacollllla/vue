<template>
  <div>{{ greeting('zzz') }}</div>
  <div>{{ greet }}</div>
  <div v-bind:class="nameClass">{{ name2 }}</div> 
  <!-- <div :class="nameClass">{{ name2 }}</div> v-bind 는 생략 가능. 콜론 ( : ) 만 사용할 수 있다. --> 
  <button class="btn" v-on:click="consoleLog">Click</button> <!--click event ( vue | v-on:click="click" / react | onClick={click})-->
  <input type="text" v-bind:value="name" /> <!-- v-bind:value="" 데이터바인딩. name의 데이터를 가져온다. -->
  <button class="btn" v-on:click="updateName">Click</button>
  <!-- <button class="btn" @click="updateName">Click</button> v-on 은 생략 가능. 골뱅이 ( @ ) 만 사용할 수 있다. -->

  <!-- v-on:click => @click --> 
  <!-- v-bind:value => :value -->

  <!-- <input type="text" :value="name" @input="updateText" /> @input="updateText" 해주고 밑에서 e.target.value 받지 않아도 v-model 해주면 양방향바인딩 됨. -->
  <!-- https://vuejs.org/guide/essentials/forms.html#v-model-with-components -->

  <input type="text" v-model="name" />
  <button class="btn" @click="onSubmit">Click</button>


</template>

<script>
  import { ref } from 'vue'; // ref 사용시 불러오기 string, number
  import { reactive } from 'vue'; // reactive 사용시 불러오기 object, array


  export default {
    setup() {
      const name = ref('coke');
      const name2 = reactive({
        id: 1
      });
      const nameClass = ref('name');

      // const name2 = ref({
      //   id: 1
      // })
      // 객체나 배열도 ref 사용할 수 있음. ref 를 사용하면 name2.value.id  이렇게 value를 꼭 넣어줘야함.

      const greeting = (name) => {
        return 'Hello, ' + name;
      }

      const greet = greeting(name);

      const consoleLog = () => {
        console.log('hellow world');
      }

      const updateName = () => {
        name.value = 'cider'; // ref
        name2.id = 2; // reactive
        nameClass.value = 'redName';
      }

      const onSubmit = () => {
        console.log(name.value);
      }

      // const updateText = (e) => {
      //   name.value = e.target.value;
      // }

      return { // 위에 선언한 변수를 리턴. 그럼 리턴한 변수는 위의 template에서 접근 가능함.
        name,
        name2,
        greeting,
        greet,
        consoleLog,
        updateName,
        nameClass,
        onSubmit,
        // updateText
      }
    }
  }
</script>

<style>
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
  }
</style>
