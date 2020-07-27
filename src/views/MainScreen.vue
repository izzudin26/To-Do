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
                  :label="languages == 'INDONESIA' ? 'Judul' : 'title'"
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
                      :label="languages == 'INDONESIA' ? 'Batas waktu' : 'Due Date'"
                      readonly
                      outlined
                      append-icon="fa-calendar"
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker v-model="todoDueDate" @input="dateMenu = false"></v-date-picker>
                </v-menu>
              </v-col>
            </v-row>
            <v-btn v-if="isUpdate" color="primary" @click="handleUpdate" elevation="0">{{languages == "INDONESIA" ? "Simpan" : "Save"}}</v-btn>
            <v-btn v-else color="success" @click="submit" elevation="0">{{languages == "INDONESIA" ? "Buat Pekerjaan" : "Create Task"}}</v-btn>
          </v-container>
      </v-card>
      <v-banner v-if="todoList.length != 0">
      <h2 class="mt-5">To Do List</h2>
      <transition-group name="fade">
      <v-card v-for="(todo, i) in todoList" :key="i" class="mt-2" outlined>
        <v-row class="mt-2 mb-2 ml-2 mr-2 pa-1">
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
      </v-banner>
      <v-banner v-if="todoComplete.length != 0">
      <h2 class="mt-5">Complete</h2>
      <transition-group name="fade">
      <v-card v-for="(todo, i) in todoComplete" :key="i" class="mt-2" outlined>
        <v-row class="mt-2 mb-2 ml-2 mr-2 pa-1">
          <v-hover v-slot:default="{ hover }" v-for="(btn,idx) in buttonStyle" :key="idx">
            <v-btn
            class="ml-4"
            icon
            x-small
            :color=" btn.icon == 'fa-check' ? btn.color : 'grey'">
            <v-icon>{{btn.icon}}</v-icon>
          </v-btn>
          </v-hover>
          <span class="ml-5">{{todo.title}}</span>
          <v-spacer></v-spacer>
          <b>{{ dateFormat(todo.dueDate)}}</b>
        </v-row>
      </v-card>
      </transition-group>
      </v-banner>
      <v-banner v-if="todoUncomplete.length != 0">
      <h2 class="mt-5">uncomplete</h2>
      <transition-group name="fade">
      <v-card v-for="(todo, i) in todoUncomplete" :key="i" class="mt-2" outlined>
        <v-row class="mt-2 mb-2 ml-2 mr-2 pa-1">
          <v-hover v-slot:default="{ hover }" v-for="(btn,idx) in buttonStyle" :key="idx">
            <v-btn
            class="ml-4"
            icon
            x-small
            :color=" btn.icon == 'fa-times' ? btn.color : 'grey'">
            <v-icon>{{btn.icon}}</v-icon>
          </v-btn>
          </v-hover>
          <span class="ml-5">{{todo.title}}</span>
          <v-spacer></v-spacer>
          <b>{{ dateFormat(todo.dueDate)}}</b>
        </v-row>
      </v-card>
      </transition-group>
      </v-banner>
    </v-container>

  </div>
</template>

<script>
export default {
  name: 'MainScreen',
  props: ['languages'],
  computed: {
    dateFormatter () {
      const lang = this.languages === 'INDONESIA' ? 'ID' : 'EN'
      const dates = this.todoDueDate ?? new Date().toString().substr(0, 10)
      const setting = {
        day: 'numeric',
        weekday: 'long',
        month: 'long',
        year: 'numeric'
      }
      return new Date(dates).toLocaleDateString(lang, setting)
    }
  },
  data: () => ({
    todoList: [],
    dateMenu: false,
    isUpdate: false,
    indexUpdate: null,
    todoTitle: '',
    todoDueDate: new Date().toISOString().substr(0, 10),
    todoComplete: [],
    todoUncomplete: [],
    buttonStyle: [
      {
        icon: 'fa-check',
        color: 'success',
        func: 'complete'
      },
      {
        icon: 'fa-times',
        color: 'red',
        func: 'uncomplete'
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
    this.todoCompleteGet()
    this.todoUncompleteGet()
  },
  methods: {
    dateFormat (value) {
      const lang = this.languages === 'INDONESIA' ? 'ID' : 'EN'
      const date = new Date(value).toISOString().substr(0, 10)
      const setting = {
        day: 'numeric',
        weekday: 'long',
        month: 'long',
        year: 'numeric'
      }
      return new Date(date).toLocaleDateString(lang, setting)
    },
    handleBtn (func, idxVal) {
      switch (func) {
        case 'complete':
          this.handleComplete(idxVal)
          break

        case 'uncomplete':
          this.handleUncomplete(idxVal)
          break

        case 'edit':
          this.handleEdit(idxVal)
          break

        case 'delete':
          this.delete(idxVal)
          break
      }
    },
    handleUpdate () {
      this.todoList[this.indexUpdate] = {
        title: this.todoTitle,
        dueDate: this.todoDueDate
      }
      this.todoTitle = ''
      this.todoDueDate = new Date().toISOString().substr(0, 10)
      this.isUpdate = false
      this.indexUpdate = null
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
    },
    handleEdit (index) {
      this.todoTitle = this.todoList[index].title
      this.todoDueDate = this.todoList[index].dueDate
      this.isUpdate = true
      this.indexUpdate = index
    },
    handleComplete (idxValue) {
      this.todoComplete.push(this.todoList[idxValue])
      this.todoList.splice(idxValue, 1)
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
      localStorage.setItem('todoComplete', JSON.stringify(this.todoComplete))
    },
    handleUncomplete (idxValue) {
      this.todoUncomplete.push(this.todoList[idxValue])
      this.todoList.splice(idxValue, 1)
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
      localStorage.setItem('todoUncomplete', JSON.stringify(this.todoUncomplete))
    },
    async todoCompleteGet () {
      const getData = await localStorage.getItem('todoComplete')
      if (getData != null) {
        this.todoComplete = JSON.parse(getData)
      }
    },
    async todoUncompleteGet () {
      const data = await localStorage.getItem('todoUncomplete')
      if (data != null) { this.todoUncomplete = JSON.parse(data) }
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
      this.todoList.push({
        title: this.todoTitle,
        dueDate: this.todoDueDate
      })
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
      this.todoTitle = ''
      this.todoDueDate = new Date().toISOString().substr(0, 10)
    },
    delete (index) {
      this.todoList.splice(index, 1)
      localStorage.setItem('todoItem', JSON.stringify(this.todoList))
    }
  }
}
</script>

<style>

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
