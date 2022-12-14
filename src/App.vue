<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <TodoHeader @addTodo="addTodo"></TodoHeader>

        <TodoList
          :todos="todos"
        ></TodoList>

        <TodoFooter
          :todos="todos"
          @checkAllTodos="checkAllTodos"
          @clearTodos="clearTodos"
        ></TodoFooter>
      </div>
    </div>
  </div>
</template>

<script>
import pubsub from 'pubsub-js';

import TodoHeader from "./components/TodoHeader";
import TodoList from "./components/TodoList";
import TodoFooter from "./components/TodoFooter";

export default {
  name: "App",
  components: { TodoHeader, TodoList, TodoFooter },

  data() {
    return {
      todos: JSON.parse(localStorage.getItem("todos")) || [],
    };
  },
  methods: {
    // 添加一个todo
    addTodo(todo) {
      this.todos.push(todo);
    },
    // 勾选或取消勾选todo项
    checkTodo(id) {
      this.todos.forEach((todo) => {
        if (todo.id === id) {
          todo.done = !todo.done;
        }
      });
    },
    // 删除一个todo项
    deleteTodo(id) {
      this.todos = this.todos.filter((todo) => {
        return todo.id != id;
      });
    },
    // 全选or取消全选
    checkAllTodos(done) {
      this.todos.forEach((todo) => {
        todo.done = done;
      });
    },
    // 删除所有已完成todo
    clearTodos() {
      this.todos = this.todos.filter((todo) => {
        return !todo.done;
      });
    },
  },
  watch: {
    todos: {
      deep: true,
      handler(value) {
        localStorage.setItem("todos", JSON.stringify(value));
      },
    },
  },
  mounted() {
    this.$bus.$on('checkTodo', this.checkTodo);
    this.$bus.$on('updateTodo', (id, name) => {
      this.todos.forEach((todo) => {
        if (todo.id === id) {
          todo.name = name;
        }
      })
    });
    this.pubIdDeleteTodo = pubsub.subscribe('deleteTodo', (_, id) => {this.deleteTodo(id)})
  },
  beforeDestroy() {
    this.$bus.$off('checkTodo');
    pubsub.unsubscribe(this.pubIdDeleteTodo);
  },
};
</script>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2),
    0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn-edit {
  color: #fff;
  background-color: skyblue;
  border: 1px solid rgb(15, 91, 121);
  margin-right: 5px;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
