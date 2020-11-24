<template>
  <div class="mb-4">
    <div class="todo-item" :class="{ stared: todo.stared }">
      <div class="handle text-muted"><i class="fas fa-ellipsis-v"></i></div>
      <div class="todo-header">
        <div class="todo-check form-check">
          <input
            type="checkbox"
            class="form-check-input"
            style="width: 24px; height: 24px"
            v-model="todo.completed"
            @change="updateStatus('completed', todo.completed)"
            :true-value="'completed'"
            :false-value="'progress'"
          />
        </div>
        <div
          class="todo-title ml-3"
          :class="{ completed: todo.completed === 'completed' }"
        >
          {{ todo.message }}
        </div>
        <div class="todo-control">
          <a href="" class="text-muted" @click.prevent="updateStatus('stared', !todo.stared)"
            ><i class="far fa-star mr-3" v-if="!todo.stared"></i>
            <i class="fas fa-star mr-3" v-else></i
          ></a>
          <a href="" class="text-muted"><i class="fas fa-pencil-alt"
          @click.prevent="editTodo"></i></a>
        </div>
      </div>
      <div class="todo-footer text-secondary">
        <i class="far fa-calendar-alt mr-3" v-if="todo.startDate || todo.endDate"></i>
        <span v-if="todo.startDate">from: {{ todo.startDate }}</span>
        <span v-if="todo.startDate && todo.endDate"> ~ </span>
        <span v-if="todo.endDate">to: {{ todo.endDate }} </span>
        <i class="far fa-comment-dots ml-3" v-if="todo.comment >0">{{todo.comment.length}}</i>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['todo'],
  name: 'Todo',
  methods: {
    updateStatus(field, state) {
      const vm = this;
      const api = `http://localhost:3000/todos/${vm.todo.id}`;
      const todo = {
        ...vm.todo,
      };
      todo[field] = state;
      vm.$http.put(api, todo).then((response) => {
        console.log(response);
        vm.$emit('getData');
      });
    },
    editTodo() {
      this.$emit('editTodo', this.todo.id);
    },
  },
};
</script>
