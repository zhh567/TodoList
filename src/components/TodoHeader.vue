<template>
  <div class="todo-header">
    <input
      type="text"
      placeholder="请输入你的任务名称，按回车键确认"
      @keyup.enter="add"
    />
  </div>
</template>

<script>
import { nanoid } from "nanoid";
 
export default {
  name: "TodoHeader",
  methods: {
    add(event) {
      // 校验数据，不能为空
      if (!event.target.value) {
        return;
      }
      // 包装成对象
      let todoObj = { id: nanoid(), name: event.target.value, done: false };
      // 清空输入框
      event.target.value = '';
      // console.log(todoObj);
      // 调用父组件方法添加数据
      this.$emit('addTodo', todoObj);
    },
  },
};
</script>

<style scoped>
/*header*/
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075),
    0 0 8px rgba(82, 168, 236, 0.6);
}
</style>