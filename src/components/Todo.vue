<template>
  <div class="container">
    <h1>Todo List</h1>
    <input
      type="text"
      placeholder="Enter todo name"
      class="new-todo"
      @keyup.enter="add_todo()"
      v-model="new_todo"
    >
    <div class="list">
      <ul>
        <li v-for="todo in todo_list" :key="todo.id">
          <div v-if="!todo.states.editing">
            <label :for="todo.id">{{ todo.name }}</label>
            <div id="options">
              <a href="#" @click="todo.states.editing = !todo.states.editing">
                <i class="mdi mdi-square-edit-outline blue"></i>
              </a>
              <a href="#" @click="remove_todo(todo)">
                <i class="mdi mdi-close red"></i>
              </a>
            </div>
          </div>
          <div v-else >
            <input type="text" v-model="editedTodo" @keyup.enter="editTodo(todo)">
            <a href="#" @click="todo.states.editing = !todo.states.editing">
              <i class="mdi mdi-close red"></i>
            </a>
          </div>
        </li>
      </ul>
      <div v-if="todo_list == '' ">
        No items...
      </div>
    </div>
  </div>
</template>

<script>

const LOCALSTOREKEY = 'todolist';

export default {
  name: 'Todo',
  data () {
    return {
      new_todo: '',
      editedTodo: null,
      editing: false,
      todo_list: [],
      checked_list: []
    }
  },
  created () {
    this.todo_list = JSON.parse(localStorage.getItem(LOCALSTOREKEY) || '[]');
  },
  methods: {

    add_todo(){
      const obj_todo = {
        id: this.todo_list.length,
        name: this.new_todo,
        states:{
          editing: false,
          visible: true,
          active: true
        }
      };

      this.todo_list.unshift(obj_todo);
      localStorage.setItem(LOCALSTOREKEY, JSON.stringify(this.todo_list));
      this.new_todo = '';
    },

    remove_todo(todo){
      this.todo_list.splice(this.todo_list.indexOf(todo), 1);
      localStorage.setItem(LOCALSTOREKEY, JSON.stringify(this.todo_list));

      /* Remove item from checked_list if exists */
      if(this.checked_list.indexOf(todo.name) !== -1)
      {
        this.checked_list.splice(
          this.checked_list.indexOf(todo.name),
          1
        );
      }
    },

    editTodo(todo){
      const index = this.todo_list.indexOf(todo)
      todo.name = this.editedTodo
      Object.assign(this.todo_list[index], todo)

      this.editedTodo = null
      this.todo_list[index].states.editing = false

      localStorage.setItem(LOCALSTOREKEY, JSON.stringify(this.todo_list));
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  @import url('https://fonts.googleapis.com/css?family=Roboto|Montserrat|Markazi+Text');
  * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Montserrat', sans-serif; }

  input[type=text]{
    padding: 5px;
    color: black;
    font-size: 15px;
    border: 1px solid #ccc;
    background-color: #fff;
  }

  .container{
    width: 350px;
    height: auto;
    font-size: 16px;
    padding: 10px;
    text-align: center;
    background-color: #ddd;
    border-radius: 10px;
    box-shadow: 4px 4px 10px rgb(150, 150, 150);
    margin: 100px auto auto 35%;
  }

  .container h1{
    font-size: 35px;
    font-weight: bold;
    letter-spacing: 5px;
    margin-bottom: 10px;
    font-family: 'Markazi Text', serif;
  }

  .container .new-todo{
    width: 90%;
  }

  .container .list{
    margin-top: 10px;
  }

  .list ul{
    list-style: none;
    text-align: left;
  }

  .list ul li{
    margin-top: 5px;
    width: 100%;
    padding: 10px;
    font-size: 18px;
    border: 0.8px solid rgb(180,180,180);
    font-family: 'Roboto', sans-serif;
  }

  .list ul li #options{
    float: right;
  }

  .list ul li a{
    text-decoration: none;
    margin-left: 5px;
  }

  .list ul li a i.red{
    color: red;
  }

  .list ul li a i.blue{
    color: blue;
  }
</style>
