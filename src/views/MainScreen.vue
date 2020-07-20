<template>
  <div>
    <v-container>
      <v-card>
        <v-card-body>
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
                  v-model="menu"
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
            <v-btn color="success" elevation="0">{{language == "ID" ? "Buat Pekerjaan" : "Create Task"}}</v-btn>
          </v-container>
        </v-card-body>
      </v-card>
    </v-container>
  </div>
</template>

<script>
export default {
  name: 'MainScreen',
  computed: {
    todoList () {
      const todoStorage = localStorage.getItem('todoItem')
      if (todoStorage == null) {
        return []
      } else {
        return todoStorage
      }
    },
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
    dateMenu: false,
    todoTitle: '',
    todoDueDate: new Date().toISOString().substr(0, 10),
    todoDescription: '',
    language: 'ID'
  })
}
</script>

<style>
</style>
