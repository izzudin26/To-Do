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
            <v-textarea
              :label="language == 'ID' ? 'Deskripsi' : 'Description'"
              v-model="todoDescription"
              outlined
              rows="2"
            ></v-textarea>
            <v-btn color="success" @click="submit" elevation="0">{{language == "ID" ? "Buat Pekerjaan" : "Create Task"}}</v-btn>
          </v-container>
      </v-card>
      <h2 class="mt-5">To Do List</h2>
      <v-card v-for="(todo, i) in todoList" :key="i" class="mt-2">
        <v-row class="mt-2 mb-3 ml-4 mr-4 pa-2">
          <v-hover v-slot:default="{ hover }">
            <v-btn icon x-small :color=" hover ? 'success' : 'grey'">
            <v-icon>fa-check</v-icon>
          </v-btn>
          </v-hover>
        </v-row>
      </v-card>
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
    todoDescription: '',
    language: 'ID'
  }),
  mounted () {
    this.todoListget()
  },
  methods: {
    todoListget () {
      const todoStorage = localStorage.getItem('todoItem')
      if (todoStorage == null) {
        this.todoList = []
      } else {
        this.todoList = JSON.parse(todoStorage)
      }
    },
    async submit () {
      const list = this.todoList
      list.push({
        title: this.todoTitle,
        dueDate: this.todoDueDate,
        description: this.todoDescription
      })
      localStorage.setItem('todoItem', JSON.stringify(list))
      this.todoList = list
      // await this.$nextTick()
      console.log(this.todoList)
    }
  }
}
</script>

<style>

</style>
