<template>
  <div class="app">
    <div class="header">Todos</div>
    <div>
      <label>New ToDo</label>
      <input v-model="newTodo" name="newTodo" />
      <nut-input v-model="newTodo" :require-show="true" label="文本" />
      <nut-button type="primary" @click="addTodo()">Add ToDo</nut-button>
    </div>
    <h2>ToDo List</h2>
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
        <span :class="{ done: todo.done }" @click="doneTodo(todo)">{{ todo.content }}</span>
        <button @click="removeTodo(index)">Remove</button>
      </li>
    </ul>
    <h4 v-if="todos.length === 0">Empty list.</h4>
  </div>
</template>

<script>
import { ref } from 'vue';
import Taro from '@tarojs/taro';
import { Input } from '@nutui/nutui-taro';
export default {
  name: 'App',
  components: {
    'nut-input': Input,
  },
  setup() {
    const newTodo = ref('');
    const defaultData = [
      {
        done: false,
        content: 'Write a blog post'
      }
    ]

    const obj = Taro.getStorageSync('todos');  // => {}
    console.log('todos', obj)


    // const todosData = Object.keys(obj).length === 0 ? defaultData : obj;


    const todosData = obj || defaultData;

    const todos = ref(todosData);

    function addTodo() {
      console.log(newTodo.value)
      if (newTodo.value) {
        todos.value.push({
          done: false,
          content: newTodo.value
        });
        newTodo.value = '';
      }
      saveData();
    }
    function doneTodo(todo) {
      todo.done = !todo.done
      saveData();
    }
    function removeTodo(index) {
      // Toast.text('删除成功');
      Taro.showToast({
        title: '删除成功',
        icon: 'success'
      })
      todos.value.splice(index, 1);
      saveData();
    }
    function saveData() {
      console.log('saveData()', todos.value)
      try {
        Taro.setStorageSync('todos', todos.value)
      } catch (e) {
        console.log(e)
      }
    }
    return {
      todos,
      newTodo,
      addTodo,
      doneTodo,
      removeTodo,
      saveData
    }
  }
}
</script>

<style lang="scss">
.app {
  padding: 0 16px;
  .header {
    font-size: 32px;
    font-weight: bold;
  }
  .done {
    text-decoration: line-through;
  }
}
</style>
