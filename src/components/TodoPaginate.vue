<template>
  <nav>
    <ul>
      <li v-if="currentPage !== 1" @click="getTodos(currentPage - 1)">Prev</li>
      <li v-for="page in numberOfPages" :key="page" :class="{ active: currentPage === page }" @click="getTodos(page)">{{ page }}</li>
      <li v-if="currentPage !== numberOfPages" @click="getTodos(currentPage + 1)">Next</li>
    </ul>
  </nav>
</template>

<script>
export default {
  props : ['currentPage', 'numberOfPages'],
  emits : ['get-todos'],
  setup(props, {emit}) {
    const getTodos = ( page ) => {
      emit('get-todos', page)
    }

    return {
      getTodos
    }

  }

}
</script>

<style>
nav ul {
    display: flex;
    justify-content: flex-start;
    padding-left: 0
  }

  nav ul li {
    padding: 8px 15px;
    list-style: none;
    border: 1px solid #ddd;
    border-left: none;
    color: #0174ff;
    font-weight: 500;
  }

  nav ul li:hover {
    background-color: #f7f7f7;
    cursor: pointer;
  }

  nav ul li:first-child {
    border-left: 1px solid #ddd;
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
  }

  nav ul li:last-child {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
  }

  .active {
    background-color: #0174ff;
    color: white;
  }
</style>