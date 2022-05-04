<template>
  <div id="app">
    <div id="root">
      <div class="todo-container">
        <div class="todo-wrap">
          <MyHeader @addtodo="addtodo" />
          <MyList :todos="todos" />
          <MyFooter
            :todos="todos"
            @checkAlltodo="checkAlltodo"
            @clearAlltodo="clearAlltodo"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MyHeader from "./components/MyHeader";
import MyList from "./components/MyList";
import MyFooter from "./components/MyFooter";
import pubsub from "pubsub-js";

export default {
  name: "App",
  components: { MyHeader, MyList, MyFooter },
  data() {
    return {
      todos: JSON.parse(localStorage.getItem("todos")) || [],
    };
  },
  methods: {
    // 添加
    addtodo(todoObj) {
      this.todos.unshift(todoObj);
    },
    // 勾选
    Checktodo(id) {
      this.todos.forEach((todo) => {
        if (todo.id === id) todo.done = !todo.done;
      });
    },
    // 编辑
    updatetodo(id, title) {
      this.todos.forEach((todo) => {
        if (todo.id === id) todo.title = title;
      });
    },
    // 删除
    deletetodo(_, id) {
      this.todos = this.todos.filter((todo) => todo.id !== id);
    },
    // 全选or全不选
    checkAlltodo(done) {
      this.todos.forEach((todo) => {
        todo.done = done;
      });
    },
    // 清除已完成
    clearAlltodo() {
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
    this.$bus.$on("Checktodo", this.Checktodo);
    // this.$bus.$on("deletetodo", this.deletetodo);
    this.$bus.$on("updatetodo", this.updatetodo);
    this.pubId = pubsub.subscribe("deletetodo", this.deletetodo);
  },
  beforeDestroy() {
    this.$bus.$off("Checktodo", this.Checktodo);
    // this.$bus.$off("deletetodo", this.deletetodo);
    this.$bus.$off("updatetodo", this.updatetodo);
    pubsub.unsubscribe(this.pubId);
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
.btn-edit {
  color: #fff;
  background-color: skyblue;
  border: 1px solid rgb(26, 77, 97);
  margin-right: 5px;
}
.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
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
