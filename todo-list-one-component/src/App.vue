<template>
  <div id="app" style="text-align: center">
    <h2>Todo List</h2>
    <input
      type="text"
      placeholder="add todo ..."
      v-model="addText"
      @keydown="keyDown"
    />
    <!-- @keypress="keyPress" -->
    <!-- @keyup="keyUp" -->
    <button @click="addTodo">ADD</button>

    <!-- <button @click="getListAll">See All</button>
    <button @click="getTodoList">See Todos</button>
    <button @click="getDoneList">See Done</button> -->

    <h4>일반 data()로 처리하기</h4>
    <table style="margin: auto">
      <thead style="background-color: lightblue; height: 40px">
        <tr>
          <th>NO.</th>
          <th style="width: 500px">Content</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(todo, index) in list"
          :key="todo.id"
          @dblclick="changeTitle(index)"
        >
          <td @click="deleteTodo(index)">{{ index + 1 }}</td>
          <td v-if="todo.changeOn">
            <input
              type="text"
              v-model="newTitle"
              :placeholder="todo.title"
              @keydown="changeTodo(index, $event)"
            />
          </td>
          <td v-else>{{ todo.title }}</td>
          <td @click="changeStatus(index)">{{ todo.completed }}</td>
        </tr>
      </tbody>
    </table>

    <hr />

    <button @click="getListAll">See All</button>
    <button @click="getTodoList">See Todos</button>
    <button @click="getDoneList">See Done</button>

    <h4>검색기능 computed로 처리하기</h4>
    <input type="text" v-model="searchKeyword" />
    <table style="margin: auto">
      <thead style="background-color: lightblue; height: 40px">
        <tr>
          <th>NO.</th>
          <th style="width: 500px">Content</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(todo, index) in getComputedTodoList" :key="todo.id">
          <td>{{ index + 1 }}</td>
          <td>{{ todo.title }}</td>
          <td>{{ todo.completed }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      list: [],
      originList: [],
      searchKeyword: "",
      addText: null,
      newTitle: "",
    };
  },

  mounted() {
    this.initList();
  },

  methods: {
    // 1. ㅁㅔ소드로 처리해서 false <-> true 케이스 변환 시 전환 안되는거 확인 -> computed 사용 / data에 originList 받아두기
    initList() {
      console.log("init list");

      this.$axios
        .get("https://jsonplaceholder.typicode.com/todos")
        .then((response) => {
          // this.originList = response.data.slice(0, 20);
          // this.list = response.data.slice(0, 20);

          let list = response.data.slice(0, 20);

          for (let i = 0; i < list.length; i++) {
            this.originList.push({
              id: list[i].id,
              title: list[i].title,
              completed: list[i].completed,
              changeOn: false,
            });

            this.list.push({
              id: list[i].id,
              title: list[i].title,
              completed: list[i].completed,
              changeOn: false,
            });
          }
        });
    },

    getListAll() {
      alert("get list all");

      this.initList();
    },

    getTodoList() {
      alert("get todo list");

      let todoList = [];

      // for (let i = 0; i < this.list.length; i++) {
      for (let i = 0; i < this.originList.length; i++) {
        // if (this.list[i].completed === false) {
        if (this.originList[i].completed === false) {
          // todoList.push(this.list[i]);
          todoList.push(this.originList[i]);
        }
      }

      this.list = todoList;
    },

    getDoneList() {
      let doneList = [];

      alert("Get Done List");

      // for (let i = 0; i < this.list.length; i++) {
      for (let i = 0; i < this.originList.length; i++) {
        // if (this.list[i].completed) {
        if (this.originList[i].completed) {
          // doneList.push(this.list[i]);
          doneList.push(this.originList[i]);
        }
      }

      this.list = doneList;
    },

    addTodo(e) {
      console.log("e", e);

      let newTodo = {
        id: this.list.length + 1,
        title: this.addText,
        completed: false,
      };

      this.list.push(newTodo);
    },

    keyUp() {
      console.log("keyUp", this.addText);
      // e.preventDefault();
      // e.stopPropagation();
    },

    keyDown(e) {
      console.log("keyDown", this.addText);
      // e.preventDefault();
      // e.stopPropagation();

      // console.log("event", e);
      if (e.keyCode === 13) {
        this.addTodo();
      }
    },

    keyPress() {
      console.log("key press", this.addText);
      // e.preventDefault();
    },

    changeStatus(index) {
      console.log("change Status", index);

      if (this.list[index].completed === false) {
        this.list[index].completed = true;
      } else {
        this.list[index].completed = false;
      }
    },

    changeTitle(index) {
      console.log(
        "changeTitle",
        index,
        this.newTitle,
        this.list[index].changeOn
      );
      this.list[index].changeOn = true;
    },

    changeTodo(index, e) {
      if (e.keyCode === 13) {
        this.list[index].title = this.newTitle;
        this.list[index].changeOn = false;
        this.newTitle = "";
      }
    },

    deleteTodo(index) {
      this.list.splice(index, 1);
    },
  },

  // 2번 강의 - 검색
  computed: {
    getComputedTodoList() {
      let newList = [];

      if (this.searchKeyword === "") {
        newList = this.list;
      } else {
        for (let i = 0; i < this.list.length; i++) {
          if (this.list[i].title.includes(this.searchKeyword)) {
            newList.push(this.list[i]);
          }
        }
      }

      return newList;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

table tbody tr td {
  padding: 3px;
}

table tbody tr:nth-child(odd) {
  background-color: #f2f2f2f2;
}

table tbody tr:hover {
  background-color: lightgreen;
  cursor: pointer;
}

button {
  margin: 10px;
  padding: 5px;
}

button:hover {
  cursor: pointer;
}
</style>
