<template>
  <div>
    <v-container>
      <v-card>
          <v-container>
            <v-row>
              <v-col md="6" xl="6" lg="6" sm="12">
                <v-text-field
                  outlined
                  v-model="todoTitle"
                  :label="language == 'ID' ? 'Judul' : 'title'"
                  append-icon="fa-edit"
                ></v-text-field>
              </v-col>
              <v-col md="6" xl="6" lg="6" sm="12">
                <v-menu
                  ref="menu"
                  v-model="dateMenu"
                  :close-on-content-click="false"
                  transition="scale-transition"
                  offset-y
                  min-width="290px"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="dateFormatter"
                      :label="language == 'ID' ? 'Batas waktu' : 'Due Date'"
                      readonly
                      outlined
                      append-icon="fa-calendar"
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker v-model="todoDueDate" @input="dateMenu = false" :locale="language"></v-date-picker>
                </v-menu>
              </v-col>
            </v-row>
            <v-btn color="success" @click="submit" elevation="0">{{language == "ID" ? "Buat Pekerjaan" : "Create Task"}}</v-btn>
          </v-container>
      </v-card>
      <h2 class="mt-5">To Do List</h2>
      <transition-group name="fade">
      <v-card v-for="(todo, i) in todoList" :key="i" class="mt-2" outlined>
        <v-row class="mt-2 mb-2 ml-3 mr-3 pa-1">
          <v-hover v-slot:default="{ hover }" v-for="(btn,idx) in buttonStyle" :key="idx">
            <v-btn
            class="ml-4"
            icon
            x-small
            :color=" hover ? btn.color : 'grey'"
            @click="handleBtn(btn.func, i)">
            <v-icon>{{btn.icon}}</v-icon>
          </v-btn>
          </v-hover>
          <span class="ml-5">{{todo.title}}</span>
          <v-spacer></v-spacer>
          <b>{{ dateFormat(todo.dueDate) }}</b>
        </v-row>
      </v-card>
      </transition-group>
    </v-container>
  </div>
</template>

<script>
export default {
  name: 'MainScreen',
  computed: {
    dateFormatter () {
      const dates = this.todoDueDate ?? new Date().toString().substr(0, 10)
      const setting = {
        day: 'numeric',
        weekday: 'long',
        month: 'long',
        year: 'numeric'
      }
      return new Date(dates).toLocaleDateString(this.language, setting)
    }
  },
  data: () => ({
    todoList: [],
    dateMenu: false,
    todoTitle: '',
    todoDueDate: new Date().toISOString().substr(0, 10),
    language: 'ID',
    todoDone: [],
    todoNotDone: [],
    buttonStyle: [
      {
        icon: 'fa-check',
        color: 'success',
        func: 'complete'
      },
      {
        icon: 'fa-times',
        color: 'red',
        func: 'complete'
      },
      {
        icon: 'fa-pencil-alt',
        color: 'yellow',
        func: 'edit'
      },
      {
        icon: 'fa-trash',
        color: 'error',
        func: 'delete'
      }
    ]
  }),
  mounted () {
    this.todoListget()
  },
  methods: {
    dateFormat (value) {
      const date = new Date(value).toISOString().substr(0, 10)
      const setting = {
        day: 'numeric',
        weekday: 'long',
        month: 'long',
        year: 'numeric'
      }
      return new Date(date).toLocaleDateString(this.language, setting)
    },
    handleBtn (func, idxVal) {
      switch (func) {
        case 'done':

          break
        case 'notDone':

          break
        case 'edit':

          break
        case 'delete':
          this.delete(idxVal)
          break
      }
    },
    todoListget () {
      const todoStorage = localStorage.getItem('todoItem')
      if (todoStorage == null) {
        this.todoList = []
      } else {
        this.todoList = JSON.parse(todoStorage)
      }
    },
    submit () {
      const list = this.todoList
      list.push({
        title: this.todoTitle,
        dueDate: this.todoDueDate
      })
      localStorage.setItem('todoItem', JSON.stringify(list))
      this.todoList = list
      this.todoTitle = ''
      this.todoDueDate = new Date().toISOString().substr(0, 10)
      this.todoDescription = ''
    },
    delete (index) {
      this.todoList.splice(index, 1)
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
    }
  }
}
</script>

<style>
.date{
  color: "#";
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
