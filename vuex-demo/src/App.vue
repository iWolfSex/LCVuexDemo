/* eslint-disable no-debugger */
<template>
  <div id="root">
    <div class="todo-container">
      <div class="todo-wrap">
        <MyHeader @addTodo="addTodo" />
        <MyList :todos="todos" />
        <MyFooter
          :todos="todos"
          @checkallTodo="checkallTodo"
          @clearAllTodo="clearAllTodo"
        />
      </div>
    </div>
  </div>
</template>

<script>
import pubsub from "pubsub-js";
import MyHeader from "./components/MyHeader.vue";
import MyFooter from "./components/MyFooter.vue";
import MyList from "./components/MyList.vue";

export default {
  name: "App",
  components: {
    MyHeader,
    MyFooter,
    MyList,
  },
  data() {
    return {
      todos: JSON.parse(localStorage.getItem("todos")) || [],
    };
  },
  methods: {
    //添加一个todo
    addTodo(todo) {
      console.log("我是App组件，我收到了数据：", todo);
      this.todos.unshift(todo);
    },
    //勾选or取消勾选一个todo
    checkeTodo(id) {
      this.todos.forEach((todo) => {
        console.log("我收到了MyItem的通知", id);
        if (todo.id === id) todo.done = !todo.done;
      });
    },
    //删除一个todo
    deleteTodo(_, id) {
      console.log('删除吗')
      this.todos = this.todos.filter((todo) => {
        return todo.id !== id;
      });
    },
    //全选or取消全选
    checkallTodo(done) {
      this.todos.forEach((todo) => {
        todo.done = done;
      });
    },
    //清空所有已经完成的Todo
    clearAllTodo() {
      // eslint-disable-next-line no-debugger
      debugger;
      this.todos = this.todos.filter((todo) => {
        return !todo.done;
      });
      console.log(this.todos);
    },
    //编辑
    updateTodo(id,title) {
      // eslint-disable-next-line no-debugger
      debugger
      this.todos.forEach((todo) => {
        if(todo.id === id) todo.title =title
      });
    },
  },
  watch: {
    // todos(value){
    //   localStorage.setItem('todos',JSON.stringify(value));

    // }
    todos: {
      deep: true,
      handler(value) {
        localStorage.setItem("todos", JSON.stringify(value));
      },
    },
  },
  mounted() {
    this.$bus.$on("checkeTodo", this.checkeTodo);
    this.$bus.$on("updateTodo", this.updateTodo);
    this.pubId = pubsub.subscribe("deleteTodo", this.deleteTodo);
  },
  beforeDestroy() {
    this.$bus.$off("checkeTodo");
    this.$bus.$off("updateTodo");
    pubsub.unsubscribe(this.pubId);
  },
};
</script>

<style>
body {
  background-color: #fff;
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
.btn-edit{
  color: #fff;
  background-color: skyblue;
  border: 1px solid skyblue;
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
