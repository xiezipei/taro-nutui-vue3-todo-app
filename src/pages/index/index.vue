<template>
  <div class="todo-app">
    <!-- 头部 -->
    <div class="todo-app-header">待办清单</div>
    <!-- 表单 -->
    <div class="todo-app-form">
      <label>新建待办</label>
      <nut-textarea
        placeholder="请输入待办内容"
        v-model="newTodo"
        class="textarea-add"
        limit-show
        max-length="20"
        rows="1"
        autosize
      />
      <nut-button type="info" shape="square" class="btn-add" block @click="addTodo()">添加待办</nut-button>
    </div>
    <!-- 列表 -->
    <div class="todo-app-list">
      <label>待办列表</label>
      <div class="cell-wrap">
        <nut-swipe v-for="(todo, index) in todos" :key="todo.content">
          <nut-cell :title="todo.content" @click="doneTodo(todo)">
            <template v-slot:icon>
              <nut-icon v-if="todo.done" name="checklist" color="green"></nut-icon>
            </template>
          </nut-cell>
          <template #right>
            <nut-button
              shape="square"
              style="height:100%"
              type="danger"
              @click.stop="removeTodo(index)"
            >删除</nut-button>
          </template>
        </nut-swipe>
      </div>
      <div class="list-empty" v-if="todos.length === 0">暂无数据</div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import Taro from '@tarojs/taro';
import { Cell, Input, Swipe, TextArea } from '@nutui/nutui-taro';
export default {
  name: 'todo-app',
  components: {
    'nut-input': Input,
    'nut-textarea': TextArea,
    'nut-cell': Cell,
    'nut-swipe': Swipe
  },
  setup() {
    const newTodo = ref('');
    const defaultData = [
      { done: false, content: 'buy apple' },
      { done: false, content: 'Learn Vue' },
      { done: false, content: 'Build something awesome' },
    ]
    const todosData = Taro.getStorageSync('todos') || defaultData;
    const todos = ref(todosData);
    function addTodo() {
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
      Taro.showToast({
        title: '删除成功',
        icon: 'success'
      })
      todos.value.splice(index, 1);
      saveData();
    }
    function saveData() {
      Taro.setStorageSync('todos', todos.value)
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
.todo-app {
  padding-bottom: 32px;
  box-sizing: border-box;
  background-color: #f7f8fa;
  height: 100vh;
  width: 100vw;
  overflow-y: auto;
  overflow-x: hidden;
  // 头部
  &-header {
    font-size: 32px;
    width: 100%;
    text-align: center;
  }
  // 表单
  &-form {
    width: 100%;
    box-sizing: border-box;
    margin-top: 8px;
    padding: 0 4px;
    label {
      padding: 0 16px;
      font-size: 24px;
    }
    .textarea-add {
      margin-top: 16px;
      color: red;
      font-family: -apple-system-font, Helvetica Neue, sans-serif;
    }
    .btn-add {
      margin-top: 8px;
      width: 100%;
    }
  }
  // 列表
  &-list {
    margin-top: 16px;
    width: 100%;
    box-sizing: border-box;
    label {
      font-size: 24px;
      padding-left: 16px;
    }
    .cell-wrap {
      width: 100%;
    }
    .list-empty {
      color: #ccc;
      font-size: 18px;
      padding-left: 16px;
      margin-top: 8px;
    }
  }
}
</style>
