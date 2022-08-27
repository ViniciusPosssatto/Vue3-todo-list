<template>
  <v-container>
    <v-row>
      <v-col
        cols="12"
        md="8"
      >
        <v-text-field
          v-model="taskTitle"
          @click:append="addTask"
          @keyup.enter="addTask"
          :counter="25"
          label="Digite aqui"
          append-icon="mdi-plus"
          required
          clearable
        ></v-text-field>
      </v-col>
    </v-row>
  </v-container>
  <div class="about pa-6" v-if="tasks.length > 0">
     <v-list 
      lines="one" 
      select-strategy="multiple"
    >
      <div 
        v-for="task in tasks" 
        :key="task.id"
      >
        <v-list-item 
          @click="doneTask(task.id)"
          value="notifications"
          :class="{ 'task-done': task.done }"
        >
          <template v-slot:prepend>
            <v-list-item-action start>
              <v-checkbox-btn :model-value="task.done"></v-checkbox-btn>
            </v-list-item-action>
          </template>
          <v-list-item-title
            :class="{ 'text-decoration-line-through' : task.done }"
          >
            {{task.title}}
          </v-list-item-title>
          <template v-slot:append>
            <v-btn
              @click.stop="deleteTask(task.id)"
              color="red"
              icon="mdi-delete"
              variant="text"
            ></v-btn>
          </template>
        </v-list-item>
      </div>
    </v-list>
  </div>
  <div v-else>
    <v-row class="center">
      <v-col
        class="d-flex child-flex center"
        cols="10"
      >
        <v-list-item-title
          class="text-red"
        >
          No tasks found...
        </v-list-item-title>
      </v-col>
      <v-col
        class="d-flex child-flex"
        cols="10"
      >
        <v-img
          :src="`https://picsum.photos/1920/1080?image=229`"
          height="300"
          cover
        >
        </v-img>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import { ref } from '@vue/reactivity'
import { onMounted } from '@vue/runtime-core'
  export default {
    setup() {
      let taskTitle = ref(null)
      let tasks = ref([])
      
      function doneTask(id) {
        let task = tasks.value.filter(task => task.id === id)[0]
        task.done = !task.done
        save_localStorage()
      };

      function deleteTask(id) {
        tasks.value = tasks.value.filter(task => task.id !== id)
        save_localStorage()
      };

      function addTask() {
        if (taskTitle.value !== null) {
          let newTask = { id: Date.now(), title: taskTitle.value, done: false }
          tasks.value.push(newTask)
          save_localStorage()
          taskTitle.value = null
        }
      };

      function  save_localStorage() {
        localStorage.setItem('tasks_list', JSON.stringify(tasks.value))
      };

      onMounted (() =>{
        tasks.value = localStorage.getItem('tasks_list') ? JSON.parse(localStorage.getItem('tasks_list')) : []
      });

      return {tasks, taskTitle, doneTask, deleteTask, addTask}
    }
  }

</script>

<style scoped>
.task-done {
  background-color: rgb(82, 205, 253, 0.4);
}
.center{
  justify-content: center;
}

</style>