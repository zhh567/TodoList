<template>
  <li>
    <label>
      <input
        type="checkbox"
        :checked="todo.done"
        @change="handleCheck(todo.id)"
      />
      <!-- <input type="checkbox" v-model="todo.done"/> 这种方法也可以实现效果，但是违反了不该修改props传值的原则 -->
      <span v-show="!todo.isEdit">{{ todo.name }}</span>
      <input
        v-show="todo.isEdit"
        type="text"
        :value="todo.name"
        @blur="handleBlur(todo, $event)"
        ref="inputTitle"
      />
    </label>
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
    <button v-show="!todo.isEdit" class="btn btn-edit" @click="handleEdit(todo)">编辑</button>
  </li>
</template>

<script>
import pubsub from "pubsub-js";

export default {
  name: "TodoItem",
  props: ["todo"],
  methods: {
    // 勾选或取消勾选
    handleCheck(id) {
      // 在App的todos数据中修改状态
      this.$bus.$emit("checkTodo", id);
    },
    // 删除
    handleDelete(id) {
      if (confirm("确认删除么？")) {
        pubsub.publish("deleteTodo", id);
      }
    },
    // 编辑功能
    handleEdit(todo) {
      if (todo.hasOwnProperty("isEdit")) {
        todo.isEdit = true;
      } else {
        // 此处不能使用 `todo.isEdit = true;` Vue会监测不到
        this.$set(todo, "isEdit", true);
      }
      // 在下一轮DOM更新完毕后再调用回调函数，当改变数据后，要基于新DOM进行更新操作时使用 nextTick 指定回调函数
      this.$nextTick(() => {
        this.$refs.inputTitle.focus();
      });
    },
    // 失去焦点时回调函数，真正执行修改逻辑
    handleBlur(todo, event) {
      todo.isEdit = false;
      if (!event.target.value.trim()) {
        alert('输入不能为空');
        return;
      }
      this.$bus.$emit('updateTodo', todo.id, event.target.value)
    },
  },
};
</script>

<style scoped>
/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  display: none;
  margin-top: 3px;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}

li:hover {
  background-color: #ddd;
}
li:hover button {
  display: block;
}
</style>