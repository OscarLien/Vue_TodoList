<template>
  <div id="app">
    <div class="bg-primary">
      <div class="container d-flex justify-content-between todo-nav">
        <a
          href=""
          @click.prevent="currentTab = 'all'"
          :class="{ active: currentTab === 'all' }"
          >My Task</a
        >
        <a
          href=""
          @click.prevent="currentTab = 'progress'"
          :class="{ active: currentTab === 'progress' }"
          >In Progress</a
        >
        <a
          href=""
          @click.prevent="currentTab = 'completed'"
          :class="{ active: currentTab === 'completed' }"
          >Completed</a
        >
      </div>
    </div>
    <div class="container my-4">
      <div class="position-relative" v-if="isNewTodo === false">
        <i
          class="fas fa-plus fa-lg position-absolute"
          style="left: 1rem; top: 1.15rem"
        ></i>
        <input
          type="text"
          class="form-control form-control-lg pl-5"
          placeholder="Add Task"
          @click="isNewTodo = true"
        />
      </div>
      <div class="mt-4">
        <EditTodoItem
          v-if="isNewTodo === true"
          @closeEdit='closeEdit'
          @getData='getData'
        ></EditTodoItem>
        <draggable
          @end="dragItem"
          v-model="todos"
          :options="{ handle: '.handle' }"
        >
          <div v-for="item in TodoData" :key="item.id">
            <TodoItem :todo="item" @getData="getData" @editTodo="editTodo"
            v-if="currentEdit.id !== item.id"></TodoItem>
            <EditTodoItem  v-if="currentEdit.id ===item.id"
            @closeEdit='closeEdit'
            @getData='getData'
            :todo="currentEdit"></EditTodoItem>
          </div>
        </draggable>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable';
import EditTodoItem from './components/EditTodoItem.vue';
import TodoItem from './components/TodoItem.vue';

export default {
  name: 'App',
  components: {
    TodoItem,
    EditTodoItem,
    draggable,
  },
  data() {
    return {
      todos: [],
      isNewTodo: false,
      sortData: [],
      currentTab: 'all',
      currentEdit: {},
    };
  },
  methods: {
    closeEdit() {
      this.currentEdit = {};
      this.isNewTodo = false;
      // this.getData();
    },
    getData() {
      const vm = this;
      let todos = [];
      vm.todos = [];
      const api = 'http://localhost:3000/todos';
      vm.$http
        .get(api)
        .then((response) => {
          todos = response.data;
          return vm.$http.get('http://localhost:3000/sort');
        })
        .then((response) => {
          vm.sortData = response.data.sort;
          if (vm.sortData) {
            vm.sortData.forEach((sortId) => {
              const todo = todos.find((item) => item.id === sortId);
              vm.todos.push(todo);
            });
            todos.forEach((todo) => {
              const hasSort = vm.sortData.some((sortId) => todo.id === sortId);
              if (!hasSort) {
                vm.todos.push(todo);
              }
            });
          } else {
            vm.todos = todos;
          }
        });
    },
    dragItem() {
      // console.log(this.cacheTodo);
      const vm = this;
      const api = 'http://localhost:3000/sort';
      const sort = vm.todos.map((item) => item.id);
      vm.$http.post(api, { sort }).then(() => {
        // console.log(response);
      });
    },
    editTodo(id) {
      const vm = this;
      this.isNewTodo = false;
      vm.currentEdit = vm.todos.find((item) => item.id === id);
    },
  },
  computed: {
    TodoData() {
      const vm = this;
      if (vm.currentTab === 'all') {
        return vm.todos;
      }
      const data = vm.todos.filter((item) => item.completed === vm.currentTab);
      // console.log(vm.currentTab);
      return data;
    },

  },
  created() {
    this.getData();
  },
};
</script>

<style>
.draggable .handle {
  cursor: move;
}
</style>
