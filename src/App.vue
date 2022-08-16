<template>
  <div id="app">
    <h1>Todo List</h1>
    <input type="text" v-on:keyup.enter="createItem" v-model.trim="input">
    <ol>
      <li class="todolist" v-for="(todo,index) in todos" :key="todo.id">
      <div class="list_content">
        <div class="list_item">
          <!-- <input type="checkbox" name="" id=""> -->
          <label for="" v-show="upList != index">{{todo.name}}</label>
          <input type="text" v-show="upList == index" v-model.trim="updateItem" v-on:keyup.enter="editItem(index)" @blur="off" ref="updateTitle">
        </div>
        <div class="function">
          <span class="update" @click="update(index)">update</span>
          <span class="delete" @click="deleteItem(index)">delete</span>
        </div>
      </div>
      </li>
    </ol>
    <router-view />
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "app",
  data() {
    return {
      input: '',
      todos: [],
      upList: -1,
      updateItem: '',
    };
  },
  methods: {
    createItem(){
      if(!this.input) return false;
      // console.log('create',this.input);
      axios.post('http://localhost:3000/todos',{
        name: this.input
      }).then(res => {
        console.log(res);
        this.input = '';
        this.todos.push(res.data);
      })
    },
    deleteItem(index){
      let target = this.todos[index]
      axios.delete(`http://localhost:3000/todos/${target.id}`).then(res => {
        this.todos.splice(index, 1);
      })
    },
    update(index){
      this.upList = index;
    },
    editItem(index){
      if(!this.updateItem) return false;
      let target = this.todos[index]
      axios.patch(`http://localhost:3000/todos/${target.id}`,{
        name: this.updateItem
      }).then(res => {
        console.log(res);
        this.updateItem = '';
        this.upList = -1;
        console.log(this.todos);
        // case1
        // this.getTodoList()
        // case2
        this.todos[index] = res.data // 這是object => 此行的用意是在前端改變todos裡面的資料，讓畫面重新render
        console.log(this.todos);
      }).catch(err => {
        console.log(err.response);
      })
    },
    // getTodoList(){
    //   axios.get('http://localhost:3000/todos')
    //   .then(res => {
    //     // console.log(res.data);
    //     this.todos = res.data;
    //   })
    //   .catch(err => {
    //     console.log(err.response);
    //   })
    // },
    off(){
      this.updateItem = '';
      this.upList = -1;
    }
  },
  mounted() {
    axios.get('http://localhost:3000/todos')
      .then(res => {
        // console.log(res.data);
        this.todos = res.data;
      })
      .catch(err => {
        console.log(err.response);
      })
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
ol{
  width: 100%;
}
li{
  line-height: 1.5;
}
.list_content{
  width: 80%;
  text-align: left;
  display: flex;
}
.list_item{
  display: flex;
}
.list_item, .function{
  width: 30%;
}
span{
  padding: 0 20px;
  cursor: pointer;
  color: blue;
  transition: .3s;
}
span:hover{
  color: rgb(255, 140, 0);
}
</style>
