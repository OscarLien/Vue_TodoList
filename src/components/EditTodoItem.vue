<template>
  <div class="mb-4">
    <div class="todo-item active">
      <div class="todo-header">
        <div class="todo-title">
          <input type="text" class="form-control" placeholder="可輸入內容" v-model="cacheTodo.message"/>
        </div>
      </div>
      <div class="todo-body">
        <div class="todo-content">
          <div class="todo-row">
            <div class="todo-icon">
              <i class="far fa-calendar-alt"></i>
            </div>
            <div>
              <label for="">Deadline</label>
              <div class="form-inline todo-form-control">
                <input type="date" class="form-control border-0" v-model="cacheTodo.startDate" />
                <input type="date" class="form-control border-0 ml-2" v-model="cacheTodo.endDate" />
              </div>
            </div>
          </div>
          <div class="todo-row">
            <div class="todo-icon">
              <i class="far fa-comment-dots"></i>
            </div>
            <div class="todo-form-control">
              <label for="">Comment</label>
              <div class="bg-white my-3 p-3">留言訊息</div>
              <div>
                <textarea
                  name=""
                  class="form-control w-100 border-0"
                  v-model="cacheTodo.comment"
                ></textarea>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="todo-brn-group">
        <button class="btn text-danger btn-lg w-50" @click="closeEdit">
          <i class="fas fa-times mr-3"></i>Cancel
        </button>
        <button class="btn btn-primary btn-lg w-50" @click="updateTodo">
          <i class="fas fa-plus mr-3"></i>Update Task
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['todo'],
  name: 'EditTodo',
  data() {
    return {
      cacheTodo: {},
      isNew: false,
      comment: '',
    };
  },
  methods: {
    updateTodo() {
      // console.log(this.cacheTodo);
      const vm = this;
      const todo = {
        ...vm.cacheTodo,
      };
      if (!vm.cacheTodo.message) {
        // alert('請輸入訊息');
        return;
      }
      if (vm.isNew) {
        const api = 'http://localhost:3000/todos';
        todo.timestamp = Math.floor(Date.now() / 1000);
        todo.stared = false;
        todo.completed = 'progress';
        // todo.comments = [];
        vm.$http.post(api, todo).then(() => {
          vm.$emit('closeEdit');
          vm.$emit('getData');
        });
      } else {
        const api = `http://localhost:3000/todos/${vm.todo.id}`;
        vm.$http.put(api, todo).then(() => {
          vm.$emit('closeEdit');
          vm.$emit('getData');
        });
      }
    },
    updateComment() {
      const vm = this;
      const api = `http://localhost:3000/todos/${vm.todo.id}`;
      if (!vm.comment) {
        return;
      }
      const todo = {
        ...vm.todo,
      };
      todo.comments.push(vm.comment);
      vm.$http.put(api, todo).then(() => {
        vm.comment = '';
        vm.$emit('getData');
      });
    },
    closeEdit() {
      this.$emit('closeEdit');
    },
  },
  watch: {
    todo() {
      // 如果為舊有資料則使用解構傳至 cacheTodo 避免物件參考特性
      this.cacheTodo = { ...this.todo };
    },
  },
  created() {
    if (!this.todo) {
      this.isNew = true;
    } else {
      this.cacheTodo = { ...this.todo };
    }
  },
};
</script>
