<template>
  <v-container>
    <v-row justify="center">
      <v-col md="6">
        <v-card class="pa-4">
          <v-col sm="12" cols="12">
            <v-text-field
              label="Todo"
              v-model="inputField"
              v-on:keyup.enter="submit"
            ></v-text-field>
          </v-col>
          <v-col sm="12" cols="12" class="text-center">
            <v-btn color="primary" @click="submit"
              >add
              <v-icon dark right> mdi-checkbox-marked-circle </v-icon>
            </v-btn>
          </v-col>
          <v-col sm="12" col="12" class="text-center">
            <v-btn
              outlined
              color="success"
              class="ma-2"
              @click="filterTodo = 'active'"
              >Active</v-btn
            >
            <v-btn
              outlined
              color="success"
              class="ma-2"
              @click="filterTodo = 'complete'"
              >complete</v-btn
            >
            <v-btn color="success" class="ma-2" @click="filterTodo = 'alldata'"
              >all data</v-btn
            >
          </v-col>
          <v-col sm="12" cols="12" class="text-center">
            <v-btn
              class="ma-2"
              outlined
              color="primary"
              v-if="!isComplete"
              @click="selectAll"
              >select all data</v-btn
            >
            <v-btn class="ma-2" color="primary" v-else @click="unselectAll"
              >unselected all data</v-btn
            >
            <v-btn class="ma-2" color="error" @click="removeAllComplete"
              >remove completed data</v-btn
            >
          </v-col>
          <v-divider></v-divider>
          <li v-for="(todo, index) in computedFilter" :key="index">
            <input
              type="checkbox"
              class="mr-2"
              :checked="todo.complete"
              @click="completeTask(todo)"
            />
            <span :class="{ completed: todo.complete }">{{ todo.title }}</span>
            <v-spacer></v-spacer>
            <v-btn class="ma-2" color="primary" @click="edit(todo, index)"
              >edit
              <v-icon dark right>mdi-pencil</v-icon>
            </v-btn>
            <v-btn color="error" @click="remove(index)"
              >delete
              <v-icon dark right> mdi-cancel </v-icon>
            </v-btn>
            <v-divider></v-divider>
          </li>
        </v-card>
      </v-col>
    </v-row>
    <v-snackbar right top v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}

      <template v-slot:action="{ attrs }">
        <v-btn color="white" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
    <v-dialog
      v-model="editDialog"
      transition="dialog-bottom-transition"
      max-width="500"
    >
      <v-card>
        <v-col>
          <v-text-field
            label="todo"
            v-model="newTodo"
            v-on:keyup.enter="update"
          ></v-text-field>
          <v-btn class="mr-2" color="primary" @click="cancelEdit">cancel</v-btn>
          <v-btn color="success" @click="update">update</v-btn>
        </v-col>
      </v-card>
    </v-dialog>
    <v-dialog v-model="deleteDialog" max-width="500px">
      <v-card>
        <v-card-title class="headline pa-6"
          >Are you sure you want to delete this item?</v-card-title
        >
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="deleted">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>
<script>
export default {
  data() {
    return {
      inputField: "",
      todos: [
        { title: "Go home", complete: false },
        { title: "Hair cut", complete: false },
      ],
      selectIndex: null,
      isEditing: false,
      isComplete: false,
      filterTodo: "",
      snackbar: false,
      snackbarText: "",
      snackbarColor: "",
      deleteDialog: false,
      deleteId: null,
      editDialog: false,
      newTodo: "",
      completed: false,
      newComplete: false,
    };
  },
  mounted() {
    if (localStorage.todos) {
      this.todos = JSON.parse(localStorage.todos);
    }
  },
  watch: {
    todos: {
      handler(newTodos) {
        localStorage.todos = JSON.stringify(newTodos);
      },
      deep: true,
    },
  },
  computed: {
    computedFilter() {
      if (this.filterTodo === "active") {
        return this.todos.filter((todo) => todo.complete === false);
      } else if (this.filterTodo === "complete") {
        return this.todos.filter((todo) => todo.complete === true);
      } else if (this.filterTodo === "alldata") {
        return this.todos;
      } else {
        return this.todos;
      }
    },
  },
  methods: {
    submit() {
      if (this.inputField === "") {
        alert("empty");
      } else {
        this.todos.push({
          title: this.inputField,
          complete: false,
        });
        this.inputField = "";
        this.snackbar = true;
        this.setSnackbar("success", "Successfully Added!");
      }
    },
    closeDelete() {
      this.deleteDialog = false;
    },
    remove(id) {
      this.deleteDialog = true;
      this.deleteId = id;
    },
    deleted() {
      this.todos.splice(this.deleteId, 1);
      this.closeDelete();
      this.snackbar = true;
      this.setSnackbar("error", "Successfully deleted!");
    },
    edit(data, id) {
      this.editDialog = true;
      // this.isEditing = true;
      this.newTodo = data.title;
      this.newComplete = data.complete;
      this.selectIndex = id;
    },
    cancelEdit() {
      this.editDialog = false;
    },
    update() {
      // this.isEditing = false;
      this.todos.splice(this.selectIndex, 1, {
        title: this.newTodo,
        complete: this.newComplete,
      });
      this.inputField = "";
      this.snackbar = true;
      this.setSnackbar("success", "Updated Complete!");
      this.editDialog = false;
    },
    completeTask(data) {
      data.complete = !data.complete;
    },
    selectAll() {
      this.isComplete = true;
      this.todos.forEach((todo) => (todo.complete = true));
    },
    unselectAll() {
      this.isComplete = false;
      this.todos.forEach((todo) => (todo.complete = false));
    },
    removeAllComplete() {
      this.todos = this.todos.filter((todo) => todo.complete === false);
      this.setSnackbar("error", "All complete data are deleted");
    },

    setSnackbar(color, msg) {
      this.snackbarText = msg;
      this.snackbarColor = color;
    },
  },
};
</script>
<style scoped>
li {
  list-style-type: none;
}
.completed {
  text-decoration: line-through;
}
</style>