<template>
  <div class="todo-footer" v-show="todos.length">
    <label>
      <!-- <input type="checkbox" :checked="isAll" @change="checkAll"/> -->
      <input type="checkbox" v-model="isAll" />
    </label>
    <span>
      <span>已完成 {{ doneTotal }}</span> / 全部 {{ todos.length }}
    </span>
    <button class="btn btn-danger" @click="clearAll">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: "TodoFooter",
  props: ["todos"],
  computed: {
    // 被勾选的todo项个数
    doneTotal() {
      return this.todos.reduce((pre, todo) => {
        return pre + (todo.done ? 1 : 0);
      }, 0);
    },
    // 是否被全选
    isAll: {
      get() {
        return this.doneTotal === this.todos.length && this.todos.length > 0;
      },
      set(checked) {
        this.$emit('checkAllTodos', checked);
      },
    },
  },
  methods: {
  //   checkAll(event){
  //     this.checkAllTodos(event.target.checked);
  //   },
    clearAll(){
      if (confirm('确定删除所有选中项？')) {
        this.$emit('clearTodos');
      }
    }
  }
};
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>